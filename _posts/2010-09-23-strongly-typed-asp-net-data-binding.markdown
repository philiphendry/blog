---
author: philiphendry
comments: true
date: 2010-09-23 20:18:44+00:00
layout: post
slug: strongly-typed-asp-net-data-binding
title: Strongly-typed ASP.NET Data-Binding
wordpress_id: 333
categories:
- ASP.NET
---

**Update :** Just thought I'd better highlight something before I get shot down! I could, of course, early-bind to data by saying DataField="<%#((Employee)Container.DataItem).Name%>" but below I was exploring ways to leverage code generation I had to make the syntax better - not sure I've done that of course, but the journey was fun!!

I was about to write the following code… again…




    
    <span style="color:#000000;">    </span><span style="color:#0000ff;"><</span><span style="color:#800000;">asp:GridView </span><span style="color:#ff0000;">ID</span><span style="color:#0000ff;">="grdSickness"</span><span style="color:#ff0000;"> runat</span><span style="color:#0000ff;">="server"</span><span style="color:#ff0000;"> AutoGenerateColumns</span><span style="color:#0000ff;">="false"</span><span style="color:#0000ff;">></span><span style="color:#000000;">
            </span><span style="color:#0000ff;"><</span><span style="color:#800000;">Columns</span><span style="color:#0000ff;">></span><span style="color:#000000;">
                </span><span style="color:#0000ff;"><</span><span style="color:#800000;">asp:BoundField </span><span style="color:#ff0000;">HeaderText</span><span style="color:#0000ff;">="Name"</span><span style="color:#ff0000;"> DataField</span><span style="color:#0000ff;">="EmployeeName"</span><span style="color:#ff0000;"> </span><span style="color:#0000ff;">/></span><span style="color:#000000;">
            </span><span style="color:#0000ff;"></</span><span style="color:#800000;">Columns</span><span style="color:#0000ff;">></span><span style="color:#000000;">
        </span><span style="color:#0000ff;"></</span><span style="color:#800000;">asp:GridView</span><span style="color:#0000ff;">></span><span style="color:#000000;">
    </span>







…but it strikes me as very ‘yesterday’ to have an un-typed DataField property. If the entity changes that’s bound to this grid I’m not going to know about it until the grid is bound and that’s way too late for me!

What I really want to see is something like this :




    
    <span style="color:#000000;">      </span><span style="color:#0000ff;"><</span><span style="color:#800000;">asp:BoundField </span><span style="color:#ff0000;">HeaderText</span><span style="color:#0000ff;">="Name"</span><span style="color:#ff0000;"> DataField</span><span style="color:#0000ff;">="<%=Entity.EmployeeName%>"</span><span style="color:#ff0000;"> </span><span style="color:#0000ff;">/></span>







Where the Entity object provides me with intellisense list of names. Now, this is by no means perfect and all I was looking for was a way to ‘inject’ the property name into the DataField attribute.


### Code Generation


And the reason this comes so easily is because I’ve already written a code generator that creates a data access layer of strongly typed calls to stored procedures and uses the sql command ‘set fmtonly on’ to interpret the result sets of the stored procedures to generate entities that can be loaded into collections all fairly automatically. All I had to do was add four lines of code (and six more for the unit test) to pump out a static inner class into my entity that returns those property names. Here’s an example entity and one sproc wrapper that was generated :




    
    <span style="color:#000000;">    </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">partial</span><span style="color:#000000;"> </span><span style="color:#0000ff;">class</span><span style="color:#000000;"> Employee : BaseEntity
        {
            </span><span style="color:#0000ff;">private</span><span style="color:#000000;"> Employee() { }
            </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> String Name { </span><span style="color:#0000ff;">get</span><span style="color:#000000;">; </span><span style="color:#0000ff;">private</span><span style="color:#000000;"> </span><span style="color:#0000ff;">set</span><span style="color:#000000;">; }
            </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> String Position { </span><span style="color:#0000ff;">get</span><span style="color:#000000;">; </span><span style="color:#0000ff;">private</span><span style="color:#000000;"> </span><span style="color:#0000ff;">set</span><span style="color:#000000;">; }
            </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> </span><span style="color:#0000ff;">class</span><span style="color:#000000;"> ColumnNames
            {
                </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> String Name { </span><span style="color:#0000ff;">get</span><span style="color:#000000;"> { </span><span style="color:#0000ff;">return</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">Name</span><span style="color:#800000;">"</span><span style="color:#000000;">; } }
                </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> String Position { </span><span style="color:#0000ff;">get</span><span style="color:#000000;"> { </span><span style="color:#0000ff;">return</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">Position</span><span style="color:#800000;">"</span><span style="color:#000000;">; } }
            }
            </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> Employee Load(IDataRecord record)
            {
                </span><span style="color:#0000ff;">return</span><span style="color:#000000;"> </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> Employee
                {
                    Name </span><span style="color:#000000;">=</span><span style="color:#000000;"> GetNullableString(record, </span><span style="color:#800000;">"</span><span style="color:#800000;">EmployeeName</span><span style="color:#800000;">"</span><span style="color:#000000;">),
                    Position </span><span style="color:#000000;">=</span><span style="color:#000000;"> GetNullableString(record, </span><span style="color:#800000;">"</span><span style="color:#800000;">EmployeePosition</span><span style="color:#800000;">"</span><span style="color:#000000;">)
                };
            }
            </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> IEnumerable</span><span style="color:#000000;"><</span><span style="color:#000000;">Employee</span><span style="color:#000000;">></span><span style="color:#000000;"> LoadCollection(IDataReader reader)
            {
                </span><span style="color:#0000ff;">return</span><span style="color:#000000;"> LoadCollection</span><span style="color:#000000;"><</span><span style="color:#000000;">Employee</span><span style="color:#000000;">></span><span style="color:#000000;">(reader, Load);
            }
        }
    
        </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">partial</span><span style="color:#000000;"> </span><span style="color:#0000ff;">class</span><span style="color:#000000;"> EmployeeDAL
        {
            </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">static</span><span style="color:#000000;"> DbDataReader spEmployeeSearch(</span><span style="color:#0000ff;">bool</span><span style="color:#000000;">?</span><span style="color:#000000;"> includeAuthorised)
            {
                Database db </span><span style="color:#000000;">=</span><span style="color:#000000;"> DatabaseFactory.CreateDatabase(</span><span style="color:#800000;">"</span><span style="color:#800000;">DefaultConnectionString</span><span style="color:#800000;">"</span><span style="color:#000000;">);
                DbCommand command </span><span style="color:#000000;">=</span><span style="color:#000000;"> db.GetStoredProcCommand(</span><span style="color:#800000;">"</span><span style="color:#800000;">[spEmployeeSearch]</span><span style="color:#800000;">"</span><span style="color:#000000;">);
                db.AddInParameter(command, </span><span style="color:#800000;">"</span><span style="color:#800000;">includeAuthorised</span><span style="color:#800000;">"</span><span style="color:#000000;">, DbType.Boolean, includeAuthorised);
                </span><span style="color:#0000ff;">return</span><span style="color:#000000;"> (DbDataReader)db.ExecuteReader(command);
            }
        }</span>







The extra bit that I’ve added to this generated is the static inner class called ColumnNames. This, I thought meant I could write this :




    
    <span style="color:#0000ff;"><</span><span style="color:#800000;">asp:BoundField </span><span style="color:#ff0000;">HeaderText</span><span style="color:#0000ff;">="Name"</span><span style="color:#ff0000;"> DataField</span><span style="color:#0000ff;">="<%=Employee.ColumnNames.Name%>"</span><span style="color:#ff0000;"> </span><span style="color:#0000ff;">/></span><span style="color:#000000;">
    
    </span>







Sideline : The code generator I’ve written uses a [text template](http://msdn.microsoft.com/en-us/library/bb126445.aspx) to integrate with Visual Studio which calls C# code I’ve written which then uses SQL Server SMO objects to connect to a database, iterate through the stored procedures and read extended properties which describe what to generate from them. I prefer keeping C# code in the text template to a minimum especially as I wanted the code generator to be fully unit tested!


### Code Expressions


Whoops… but I forgot – you can’t but an expression in a WebControl attribute like that and I got this error at runtime :




    
    <span style="color:#000000;">{</span><span style="color:#800000;">"</span><span style="color:#800000;">A field or property with the name '<%Employee.ColumnNames.Name%>' was not found on the selected data source.</span><span style="color:#800000;">"</span><span style="color:#000000;">}
    
    </span>







Luck would have it I’ve already solved this one before. An extensibility point in ASP.NET allows me to create a new expression processor. It’s easier just to show the code and how to use it. Here’s the code :




    
    <span style="color:#0000ff;">namespace</span><span style="color:#000000;"> Utility.Web
    {
        [ExpressionPrefix(</span><span style="color:#800000;">"</span><span style="color:#800000;">Code</span><span style="color:#800000;">"</span><span style="color:#000000;">)]
        </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">class</span><span style="color:#000000;"> CodeExpressionBuilder : ExpressionBuilder
        {
            </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> </span><span style="color:#0000ff;">override</span><span style="color:#000000;"> CodeExpression GetCodeExpression(BoundPropertyEntry entry,
               </span><span style="color:#0000ff;">object</span><span style="color:#000000;"> parsedData, ExpressionBuilderContext context)
            {
                </span><span style="color:#0000ff;">return</span><span style="color:#000000;"> </span><span style="color:#0000ff;">new</span><span style="color:#000000;"> CodeSnippetExpression(entry.Expression);
            }
        }
    }</span>







Which has to be registered in the web.config :




    
    <span style="color:#000000;"><?</span><span style="color:#000000;">xml version</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">1.0</span><span style="color:#800000;">"</span><span style="color:#000000;">?></span><span style="color:#000000;">
    </span><span style="color:#000000;"><</span><span style="color:#000000;">configuration</span><span style="color:#000000;">></span><span style="color:#000000;">
        </span><span style="color:#000000;"><</span><span style="color:#000000;">system.web</span><span style="color:#000000;">></span><span style="color:#000000;">
            </span><span style="color:#000000;"><</span><span style="color:#000000;">compilation</span><span style="color:#000000;">></span><span style="color:#000000;">
                </span><span style="color:#000000;"><</span><span style="color:#000000;">expressionBuilders</span><span style="color:#000000;">></span><span style="color:#000000;">
                    </span><span style="color:#000000;"><</span><span style="color:#000000;">add expressionPrefix</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">Code</span><span style="color:#800000;">"</span><span style="color:#000000;"> type</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">Utility.Web.CodeExpressionBuilder, Utility.Web</span><span style="color:#800000;">"</span><span style="color:#000000;">/></span><span style="color:#000000;">
                </span><span style="color:#000000;"></</span><span style="color:#000000;">expressionBuilders</span><span style="color:#000000;">></span><span style="color:#000000;">
            </span><span style="color:#000000;"></</span><span style="color:#000000;">compilation</span><span style="color:#000000;">></span><span style="color:#000000;">
        </span><span style="color:#000000;"></</span><span style="color:#000000;">system.web</span><span style="color:#000000;">></span><span style="color:#000000;">
    </span><span style="color:#000000;"></</span><span style="color:#000000;">configuration</span><span style="color:#000000;">></span><span style="color:#000000;">
    </span>







Now I can write the final version of my binding like this :




    
    <span style="color:#000000;">    </span><span style="color:#0000ff;"><</span><span style="color:#800000;">asp:BoundField </span><span style="color:#ff0000;">HeaderText</span><span style="color:#0000ff;">="Name"</span><span style="color:#ff0000;"> DataField</span><span style="color:#0000ff;">="<%$ Code : Employee.ColumnNames.Name%>"</span><span style="color:#ff0000;"> </span><span style="color:#0000ff;">/></span><span style="color:#000000;">
        </span>







That might seem like a lot of effort, but I now have a system whereby if I change any column in the result set of a stored procedure I’ll get a compile error where the entity is bound to a UI element and, to be honest, this blog post probably took longer to write!!

Oh, one caveat, I only bind directly to entities generated against a sproc for trivial CRUD like screens. Where it gets more complicated I would generate a model class and transform the entities into it in the same manner I would for ASP.NET MVC apps.
