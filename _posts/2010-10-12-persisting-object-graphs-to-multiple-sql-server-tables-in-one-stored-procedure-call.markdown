---
author: philiphendry
comments: true
date: 2010-10-12 19:52:06+00:00
layout: post
slug: persisting-object-graphs-to-multiple-sql-server-tables-in-one-stored-procedure-call
title: Persisting Object Graphs/Collections to one or more SQL Server Tables in one
  Stored Procedure Call
wordpress_id: 337
tags:
- sql-server
- t-sql
---

One of the methods I’ve used on a number of occasions to solve performance issues in an application is to reduce a number of calls to the database into one call. Using the classic order header and order detail example, rather than looping through each order and call the database for each item, I would prefer to pass the entire set of order detail to the database and persist it to the database with one T-Sql call. Taking it a step further, and possibly unnecessarily in this example, it would also be possible to pass the order header in the same call.

Now before I continue with the solution below which works in SQL Server 2005 and above, there is a new feature introduced in SQL Server 2008 allowing for [table valued parameters](http://msdn.microsoft.com/en-us/library/bb510489.aspx) to be added to stored procedures which accomplish similar solution.


### A Solution


In my particular data object I have a method, SaveDetail, that is passed a graph of objects which is then converted into XML.

```
public static void SaveDetail(ScheduleDto schedule) 
{ 
	StoredProcedureDAL.LM_Schedule_SaveXML(ConvertToXml(schedule).ToString(SaveOptions.DisableFormatting)); 
}  

private static XElement ConvertToXml(ScheduleDto schedule) { 
	return 
	new XElement("Schedule", 
		new XAttribute("Date", schedule.Date), 
		from jobArea in schedule.JobAreas 
		from employee in jobArea.Employees 
		select new XElement("Emp", 
			new XAttribute("No", employee.Number),
			employee.Schedule.Select((item, index) => 
				new XElement("W", // WorkTpe 
					new XAttribute("N", item), 
					new XAttribute("T", ConvertFloatTo24HrTimeString(index * schedule.StepSize))
				) 
			) 
		) 
	); 
}
```

The call to StoredProcedureDAL.LM_Schedule_SaveXML is not shown here and is automatically generated from the stored procedure definitions in the database. The ScheduleDto is a hierarchical object model which, in part, is converted to xml by the ConvertToXml method using Linq to Xml. Notice that a lot of the element names are very short and attributes are used heavily since the xml will be much shorter and it’s important to reduce the size of the data being posted to the database server.

The stored procedure that is called above is shown below although it isn’t complete but highlights the details required to ‘shred’ the xml into a recordset. The key to ‘shredding’ the data is taking the xml typed parameter and using the [nodes](http://msdn.microsoft.com/en-us/library/ms188282.aspx) method to return relational data. The parameter to nodes is [XQuery](http://msdn.microsoft.com/en-us/library/ms189075.aspx) and it’s best to drill down to the deepest elements in the XML since it’s easier to use parent paths to access data higher in the hierarchy… and that neatly leads on to accessing the data which is shown in the select below. The T(c) syntax provisions Table and Columns names which are then used in the Select. The [T.c.value](http://msdn.microsoft.com/en-us/library/ms178030.aspx) method accepts another XQuery (and here I’ve used parent paths, attributes and indexes) and the type to convert the xml into. I used to join directly to @xml.nodes() but as the comment in the code indicates, I’ve found it’s quicker to use an inner query or, if you prefer, a CTE – I’m afraid I’ve not tried to get to the bottom of why this is so slow.
    
```
CREATE PROCEDURE [dbo].[LM_Schedule_SaveXML]
    @xml XML
AS
begin
    insert into [LM_ScheduleDetails]( [Date], [EmployeeID], [Time], [WorkType] )
    select
        @date,
        e.EmployeeId,
        x.WorkTypeTime,
        spwt.Value
    from (
        -- Fetch the data from the xml in the inner select. This seems to
        -- be significantly quicker than trying to do everything in one select.    
        select
            T.c.value('../@No[1]', 'int') as EmployeeNumber,
            T.c.value('@T[1]', 'datetime') as WorkTypeTime,
            T.c.value('@N[1]', 'nvarchar(1)') as WorkTypeName
        from @xml.nodes('/Schedule/Emp/W') as T(c)
    ) x
    join LM_Employees e on e.PayrollNo = x.EmployeeNumber
    join LM_ShiftPatternsWorkTypes spwt on spwt.Name = x.WorkTypeName
end
GO
```

And that’s it. In the particular scenario I used this very effectively to provide near instant saving of data for something that would have taken a lot, lot longer to save with multiple calls. I like this solution too since the technology needed to facilitate this is simple to understand and very compact and therefore quick and easy to maintain.
