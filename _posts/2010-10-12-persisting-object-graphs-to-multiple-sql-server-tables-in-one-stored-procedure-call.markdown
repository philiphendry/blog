---
author: philiphendry
comments: true
date: 2010-10-12 19:52:06+00:00
layout: post
slug: persisting-object-graphs-to-multiple-sql-server-tables-in-one-stored-procedure-call
title: Persisting Object Graphs/Collections to one or more SQL Server Tables in one
  Stored Procedure Call
wordpress_id: 337
categories:
- SQL Server
- T-SQL
---

One of the methods I’ve used on a number of occasions to solve performance issues in an application is to reduce a number of calls to the database into one call. Using the classic order header and order detail example, rather than looping through each order and call the database for each item, I would prefer to pass the entire set of order detail to the database and persist it to the database with one T-Sql call. Taking it a step further, and possibly unnecessarily in this example, it would also be possible to pass the order header in the same call.

Now before I continue with the solution below which works in SQL Server 2005 and above, there is a new feature introduced in SQL Server 2008 allowing for [table valued parameters](http://msdn.microsoft.com/en-us/library/bb510489.aspx) to be added to stored procedures which accomplish similar solution.


### A Solution


In my particular data object I have a method, SaveDetail, that is passed a graph of objects which is then converted into XML.




    
    <span style="color:#000000;">   </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> </span><span style="color:#0000ff;">void</span><span style="color:#000000;"> SaveDetail(ScheduleDto schedule)
       {
           StoredProcedureDAL.LM_Schedule_SaveXML(ConvertToXml(schedule).ToString(SaveOptions.DisableFormatting));
       }
    
       </span><span style="color:#0000ff;">private</span><span style="color:#000000;"> </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> XElement ConvertToXml(ScheduleDto schedule)
       {
           </span><span style="color:#0000ff;">return</span><span style="color:#000000;">
               </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> XElement(</span><span style="color:#800000;">"</span><span style="color:#800000;">Schedule</span><span style="color:#800000;">"</span><span style="color:#000000;">,
                   </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> XAttribute(</span><span style="color:#800000;">"</span><span style="color:#800000;">Date</span><span style="color:#800000;">"</span><span style="color:#000000;">, schedule.Date),
                   from jobArea </span><span style="color:#0000ff;">in</span><span style="color:#000000;"> schedule.JobAreas
                   from employee </span><span style="color:#0000ff;">in</span><span style="color:#000000;"> jobArea.Employees
                   select </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> XElement(</span><span style="color:#800000;">"</span><span style="color:#800000;">Emp</span><span style="color:#800000;">"</span><span style="color:#000000;">,
                       </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> XAttribute(</span><span style="color:#800000;">"</span><span style="color:#800000;">No</span><span style="color:#800000;">"</span><span style="color:#000000;">, employee.Number),
                       employee.Schedule.Select((item, index) </span><span style="color:#000000;">=></span><span style="color:#000000;">
                           </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> XElement(</span><span style="color:#800000;">"</span><span style="color:#800000;">W</span><span style="color:#800000;">"</span><span style="color:#000000;">, </span><span style="color:#008000;">//</span><span style="color:#008000;"> WorkTpe</span><span style="color:#008000;">
    </span><span style="color:#000000;">                           </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> XAttribute(</span><span style="color:#800000;">"</span><span style="color:#800000;">N</span><span style="color:#800000;">"</span><span style="color:#000000;">, item),
                               </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> XAttribute(</span><span style="color:#800000;">"</span><span style="color:#800000;">T</span><span style="color:#800000;">"</span><span style="color:#000000;">, ConvertFloatTo24HrTimeString(index </span><span style="color:#000000;">*</span><span style="color:#000000;"> schedule.StepSize)))
                           )
                   )
               );
       }
    </span>







The call to StoredProcedureDAL.LM_Schedule_SaveXML is not shown here and is automatically generated from the stored procedure definitions in the database. The ScheduleDto is a hierarchical object model which, in part, is converted to xml by the ConvertToXml method using Linq to Xml. Notice that a lot of the element names are very short and attributes are used heavily since the xml will be much shorter and it’s important to reduce the size of the data being posted to the database server.

The stored procedure that is called above is shown below although it isn’t complete but highlights the details required to ‘shred’ the xml into a recordset. The key to ‘shredding’ the data is taking the xml typed parameter and using the [nodes](http://msdn.microsoft.com/en-us/library/ms188282.aspx) method to return relational data. The parameter to nodes is [XQuery](http://msdn.microsoft.com/en-us/library/ms189075.aspx) and it’s best to drill down to the deepest elements in the XML since it’s easier to use parent paths to access data higher in the hierarchy… and that neatly leads on to accessing the data which is shown in the select below. The T(c) syntax provisions Table and Columns names which are then used in the Select. The [T.c.value](http://msdn.microsoft.com/en-us/library/ms178030.aspx) method accepts another XQuery (and here I’ve used parent paths, attributes and indexes) and the type to convert the xml into. I used to join directly to @xml.nodes() but as the comment in the code indicates, I’ve found it’s quicker to use an inner query or, if you prefer, a CTE – I’m afraid I’ve not tried to get to the bottom of why this is so slow.




    
    <span style="color:#0000ff;">CREATE</span><span style="color:#000000;"> </span><span style="color:#0000ff;">PROCEDURE</span><span style="color:#000000;"> </span><span style="color:#ff0000;">[</span><span style="color:#ff0000;">dbo</span><span style="color:#ff0000;">]</span><span style="color:#000000;">.</span><span style="color:#ff0000;">[</span><span style="color:#ff0000;">LM_Schedule_SaveXML</span><span style="color:#ff0000;">]</span><span style="color:#000000;">
        </span><span style="color:#008000;">@xml</span><span style="color:#000000;"> XML
    </span><span style="color:#0000ff;">AS</span><span style="color:#000000;">
    </span><span style="color:#0000ff;">begin</span><span style="color:#000000;">
        </span><span style="color:#0000ff;">insert</span><span style="color:#000000;"> </span><span style="color:#0000ff;">into</span><span style="color:#000000;"> </span><span style="color:#ff0000;">[</span><span style="color:#ff0000;">LM_ScheduleDetails</span><span style="color:#ff0000;">]</span><span style="color:#000000;">( </span><span style="color:#ff0000;">[</span><span style="color:#ff0000;">Date</span><span style="color:#ff0000;">]</span><span style="color:#000000;">, </span><span style="color:#ff0000;">[</span><span style="color:#ff0000;">EmployeeID</span><span style="color:#ff0000;">]</span><span style="color:#000000;">, </span><span style="color:#ff0000;">[</span><span style="color:#ff0000;">Time</span><span style="color:#ff0000;">]</span><span style="color:#000000;">, </span><span style="color:#ff0000;">[</span><span style="color:#ff0000;">WorkType</span><span style="color:#ff0000;">]</span><span style="color:#000000;"> )
        </span><span style="color:#0000ff;">select</span><span style="color:#000000;">
            </span><span style="color:#008000;">@date</span><span style="color:#000000;">,
            e.EmployeeId,
            x.WorkTypeTime,
            spwt.Value
        </span><span style="color:#0000ff;">from</span><span style="color:#000000;"> (
            </span><span style="color:#008080;">--</span><span style="color:#008080;"> Fetch the data from the xml in the inner select. This seems to</span><span style="color:#008080;">
    </span><span style="color:#000000;">        </span><span style="color:#008080;">--</span><span style="color:#008080;"> be significantly quicker than trying to do everything in one select.    </span><span style="color:#008080;">
    </span><span style="color:#000000;">        </span><span style="color:#0000ff;">select</span><span style="color:#000000;">
                T.c.value(</span><span style="color:#ff0000;">'</span><span style="color:#ff0000;">../@No[1]</span><span style="color:#ff0000;">'</span><span style="color:#000000;">, </span><span style="color:#ff0000;">'</span><span style="color:#ff0000;">int</span><span style="color:#ff0000;">'</span><span style="color:#000000;">) </span><span style="color:#0000ff;">as</span><span style="color:#000000;"> EmployeeNumber,
                T.c.value(</span><span style="color:#ff0000;">'</span><span style="color:#ff0000;">@T[1]</span><span style="color:#ff0000;">'</span><span style="color:#000000;">, </span><span style="color:#ff0000;">'</span><span style="color:#ff0000;">datetime</span><span style="color:#ff0000;">'</span><span style="color:#000000;">) </span><span style="color:#0000ff;">as</span><span style="color:#000000;"> WorkTypeTime,
                T.c.value(</span><span style="color:#ff0000;">'</span><span style="color:#ff0000;">@N[1]</span><span style="color:#ff0000;">'</span><span style="color:#000000;">, </span><span style="color:#ff0000;">'</span><span style="color:#ff0000;">nvarchar(1)</span><span style="color:#ff0000;">'</span><span style="color:#000000;">) </span><span style="color:#0000ff;">as</span><span style="color:#000000;"> WorkTypeName
            </span><span style="color:#0000ff;">from</span><span style="color:#000000;"> </span><span style="color:#008000;">@xml</span><span style="color:#000000;">.nodes(</span><span style="color:#ff0000;">'</span><span style="color:#ff0000;">/Schedule/Emp/W</span><span style="color:#ff0000;">'</span><span style="color:#000000;">) </span><span style="color:#0000ff;">as</span><span style="color:#000000;"> T(c)
        ) x
        </span><span style="color:#808080;">join</span><span style="color:#000000;"> LM_Employees e </span><span style="color:#0000ff;">on</span><span style="color:#000000;"> e.PayrollNo </span><span style="color:#808080;">=</span><span style="color:#000000;"> x.EmployeeNumber
        </span><span style="color:#808080;">join</span><span style="color:#000000;"> LM_ShiftPatternsWorkTypes spwt </span><span style="color:#0000ff;">on</span><span style="color:#000000;"> spwt.Name </span><span style="color:#808080;">=</span><span style="color:#000000;"> x.WorkTypeName
    </span><span style="color:#0000ff;">end</span><span style="color:#000000;">
    </span><span style="color:#0000ff;">GO</span><span style="color:#000000;">
    </span>







And that’s it. In the particular scenario I used this very effectively to provide near instant saving of data for something that would have taken a lot, lot longer to save with multiple calls. I like this solution too since the technology needed to facilitate this is simple to understand and very compact and therefore quick and easy to maintain.
