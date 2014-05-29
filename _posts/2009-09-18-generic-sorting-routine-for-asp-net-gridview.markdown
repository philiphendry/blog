---
author: philiphendry
comments: true
date: 2009-09-18 19:41:28+00:00
layout: post
slug: generic-sorting-routine-for-asp-net-gridview
title: Generic Sorting Routine for ASP.NET GridView
wordpress_id: 288
categories:
- ASP.NET
- Dev Problem
- LINQ
---

I noticed a lot of code in the current project I’m working on which looked something like this :

 
    
```
switch (sortExpression)
{
    case "Name":
        items.OrderBy(i => i.Name);
        break;
    case "Date":
        items.OrderBy(i => i.Date);
        break;
    case "Cost":
        items.OrderBy(i => i.Cost);
        break;
}
```




This was then repeated all over again for descending orders!! I’ve now replaced it with something like this :




    
```
private void Populate(string sortExpression)
{
   var items = GetData();
   gvList.DataSource = CreateValueList(items).OrderByExpression(new OrderByExpression(sortExpression));
   gvList.DataBind();  
}
```





The sort expression is handled by the GridView code such that AllowSorting is turned on and the OnSorting event is wired up to a method :




    
```
<asp:GridView ID="gvList" runat="server" AllowSorting="True" OnSorting="gvList_Sorting" DataKeyNames="ID">
   <Columns>
       <asp:TemplateField HeaderText="Name" SortExpression="Name">
           <ItemTemplate>
               <asp:Label Text='<%# DataBinder.Eval(Container.DataItem, "Name") %>' runat="server" ID="lblName" />
           </ItemTemplate>
       </asp:TemplateField>
   </Columns>
</asp:GridView>
```





The code-behind deals with the sorting events and calls populate :






  
    
```
protected void gvList_Sorting(object sender, GridViewSortEventArgs e)
  {
     Populate(e.SortExpression + " " + GetSortDirection(e.SortExpression));
  }
  
  private string GetSortDirection(string column)
  {
  
     // By default, set the sort direction to ascending.
     string sortDirection = "ASC";
  
     // Retrieve the last column that was sorted.
     string sortExpression = ViewState["SortExpression"] as string;
  
     if (sortExpression != null)
     {
         // Check if the same column is being sorted.
         // Otherwise, the default value can be returned.
         if (sortExpression == column)
         {
             string lastDirection = ViewState["SortDirection"] as string;
             if ((lastDirection != null) && (lastDirection == "ASC"))
             {
                 sortDirection = "DESC";
             }
         }
     }
  
     // Save new values in ViewState.
     ViewState["SortDirection"] = sortDirection;
     ViewState["SortExpression"] = column;
  
     return sortDirection;
  }
  ```








What this all boils down to is the _OrderByExpression()_ extension method called in the _Populate()_ method above:




    
```
 public static IEnumerable<TSource> OrderByExpression<TSource>(this IEnumerable<TSource> data, OrderByExpression expression)
 {
     string sortOrderMethod = (expression.SortOrder == SortOrderEnum.Ascending) ? "OrderBy" : "OrderByDescending";

     // data.OrderBy(o => o.propertyname);   
     var dataAsQueryable = data.AsQueryable<TSource>();
     ParameterExpression lambdaParameter = Expression.Parameter(typeof(TSource), "o");
     MemberExpression member = Expression.PropertyOrField(lambdaParameter, expression.SortProperties[0]);
     LambdaExpression lambda = Expression.Lambda(member, lambdaParameter);
     Type[] argumentTypes = { dataAsQueryable.ElementType, lambda.Body.Type };
     MethodCallExpression orderBy = Expression.Call(typeof(Queryable), sortOrderMethod, argumentTypes, dataAsQueryable.Expression, lambda);
     return dataAsQueryable.Provider.CreateQuery<TSource>(orderBy);
 }
 ```





I have to say thanks to Joseph Albahari and Ben Albahari of [LinqPad](www.linqpad.net) fame since I found this solution to my problems (after getting within so close through my own efforts whilst our internet connection was down in the office!!) in the samples that came with LinqPad.





The code above creates a dynamic Linq expression then executes it through the queryables provider – although this only occurs once the whole linq expression is enumerated.





The last few modifications I need to make require the order by to cope with multiple properties (The _OrderByExpression.SortProperties_ class already supports multiple properties but I’m only taking the first at the moment) and I need to ensure that the query always deferred since composable against a database – this is important since I’ve yet to consider paging in the GridView and I want to make sure that specifiying _.Skip(n).Take(m)_ can be applied after the ordering but the Linq expression be composed as SQL and executed against the database in one go otherwise I’ll be returning all the rows to the application layer before extracting just the required rows for the current page.
