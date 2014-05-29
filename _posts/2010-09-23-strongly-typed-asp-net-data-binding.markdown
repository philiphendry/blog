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




    
```
<asp:GridView ID="grdSickness" runat="server" AutoGenerateColumns="false">
    <Columns>
        <asp:BoundField HeaderText="Name" DataField="EmployeeName" />
    <Columns>
<asp:GridView>

```






…but it strikes me as very ‘yesterday’ to have an un-typed DataField property. If the entity changes that’s bound to this grid I’m not going to know about it until the grid is bound and that’s way too late for me!

What I really want to see is something like this :




    
```
<asp:BoundField HeaderText="Name" DataField="<%=Entity.EmployeeName%>" />
```






Where the Entity object provides me with intellisense list of names. Now, this is by no means perfect and all I was looking for was a way to ‘inject’ the property name into the DataField attribute.


### Code Generation


And the reason this comes so easily is because I’ve already written a code generator that creates a data access layer of strongly typed calls to stored procedures and uses the sql command ‘set fmtonly on’ to interpret the result sets of the stored procedures to generate entities that can be loaded into collections all fairly automatically. All I had to do was add four lines of code (and six more for the unit test) to pump out a static inner class into my entity that returns those property names. Here’s an example entity and one sproc wrapper that was generated :

```
public partial class Employee : BaseEntity
{
    private Employee() { }
    public String Name { get; private set; }
    public String Position { get; private set; }
    public static class ColumnNames
    {
        public static String Name { get { return "Name"; } }
        public static String Position { get { return "Position"; } }
    }
    public static Employee Load(IDataRecord record)
    {
        return new Employee
        {
            Name = GetNullableString(record, "EmployeeName"),
            Position = GetNullableString(record, "EmployeePosition")
        };
    }
    public static IEnumerable<Employee> LoadCollection(IDataReader reader)
    {
        return LoadCollection<Employee>(reader, Load);
    }
}

public partial class EmployeeDAL
{
    public static DbDataReader spEmployeeSearch(bool? includeAuthorised)
    {
        Database db = DatabaseFactory.CreateDatabase("DefaultConnectionString");
        DbCommand command = db.GetStoredProcCommand("[spEmployeeSearch]");
        db.AddInParameter(command, "includeAuthorised", DbType.Boolean, includeAuthorised);
        return (DbDataReader)db.ExecuteReader(command);
    }
}
```


The extra bit that I’ve added to this generated is the static inner class called ColumnNames. This, I thought meant I could write this :

```
<asp:BoundField HeaderText="Name" DataField="<%=Employee.ColumnNames.Name%>" />
```

Sideline : The code generator I’ve written uses a [text template](http://msdn.microsoft.com/en-us/library/bb126445.aspx) to integrate with Visual Studio which calls C# code I’ve written which then uses SQL Server SMO objects to connect to a database, iterate through the stored procedures and read extended properties which describe what to generate from them. I prefer keeping C# code in the text template to a minimum especially as I wanted the code generator to be fully unit tested!


### Code Expressions


Whoops… but I forgot – you can’t but an expression in a WebControl attribute like that and I got this error at runtime :

```
{
	"A field or property with the name '<%Employee.ColumnNames.Name%>' was not found on the selected data source."
}
```

Luck would have it I’ve already solved this one before. An extensibility point in ASP.NET allows me to create a new expression processor. It’s easier just to show the code and how to use it. Here’s the code :

```
namespace Utility.Web
{
    [ExpressionPrefix("Code")]
    public class CodeExpressionBuilder : ExpressionBuilder
    {
        public override CodeExpression GetCodeExpression(BoundPropertyEntry entry,
           object parsedData, ExpressionBuilderContext context)
        {
            return new CodeSnippetExpression(entry.Expression);
        }
    }
}
```

Which has to be registered in the web.config :

```
<?xml version="1.0"?>
<configuration>
    <system.web>
        <compilation>
            <expressionBuilders>
                <add expressionPrefix="Code" type="Utility.Web.CodeExpressionBuilder, Utility.Web"/>
            </expressionBuilders>
        </compilation>
    </system.web>
</configuration>
```

Now I can write the final version of my binding like this :

```
<asp:BoundField HeaderText="Name" DataField="<%$ Code : Employee.ColumnNames.Name%>" />
```

That might seem like a lot of effort, but I now have a system whereby if I change any column in the result set of a stored procedure I’ll get a compile error where the entity is bound to a UI element and, to be honest, this blog post probably took longer to write!!

Oh, one caveat, I only bind directly to entities generated against a sproc for trivial CRUD like screens. Where it gets more complicated I would generate a model class and transform the entities into it in the same manner I would for ASP.NET MVC apps.
