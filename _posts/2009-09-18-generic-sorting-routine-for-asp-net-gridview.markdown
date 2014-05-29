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

 
    
    <span style="color:#0000FF;">switch</span><span style="color:#000000;"> (sortExpression)
    {
        </span><span style="color:#0000FF;">case</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">Name</span><span style="color:#800000;">"</span><span style="color:#000000;">:
            items.OrderBy(i </span><span style="color:#000000;">=></span><span style="color:#000000;"> i.Name);
            </span><span style="color:#0000FF;">break</span><span style="color:#000000;">;
        </span><span style="color:#0000FF;">case</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">Date</span><span style="color:#800000;">"</span><span style="color:#000000;">:
            items.OrderBy(i </span><span style="color:#000000;">=></span><span style="color:#000000;"> i.Date);
            </span><span style="color:#0000FF;">break</span><span style="color:#000000;">;
        </span><span style="color:#0000FF;">case</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">Cost</span><span style="color:#800000;">"</span><span style="color:#000000;">:
            items.OrderBy(i </span><span style="color:#000000;">=></span><span style="color:#000000;"> i.Cost);
            </span><span style="color:#0000FF;">break</span><span style="color:#000000;">;
    }</span>





This was then repeated all over again for descending orders!! I’ve now replaced it with something like this :




    
    <span style="color:#0000FF;">private</span><span style="color:#000000;"> </span><span style="color:#0000FF;">void</span><span style="color:#000000;"> Populate(</span><span style="color:#0000FF;">string</span><span style="color:#000000;"> sortExpression)
    {
       var items </span><span style="color:#000000;">=</span><span style="color:#000000;"> GetData();
       gvList.DataSource </span><span style="color:#000000;">=</span><span style="color:#000000;"> CreateValueList(items).OrderByExpression(</span><span style="color:#0000FF;">new</span><span style="color:#000000;"> OrderByExpression(sortExpression));
       gvList.DataBind();  
    }</span>





The sort expression is handled by the GridView code such that AllowSorting is turned on and the OnSorting event is wired up to a method :




    
    <span style="color:#000000;"><</span><span style="color:#000000;">asp:GridView ID</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">gvList</span><span style="color:#800000;">"</span><span style="color:#000000;"> runat</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">server</span><span style="color:#800000;">"</span><span style="color:#000000;"> AllowSorting</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">True</span><span style="color:#800000;">"</span><span style="color:#000000;"> OnSorting</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">gvList_Sorting</span><span style="color:#800000;">"</span><span style="color:#000000;"> DataKeyNames</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">ID</span><span style="color:#800000;">"</span><span style="color:#000000;">></span><span style="color:#000000;">
       </span><span style="color:#000000;"><</span><span style="color:#000000;">Columns</span><span style="color:#000000;">></span><span style="color:#000000;">
           </span><span style="color:#000000;"><</span><span style="color:#000000;">asp:TemplateField HeaderText</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">Name</span><span style="color:#800000;">"</span><span style="color:#000000;"> SortExpression</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">Name</span><span style="color:#800000;">"</span><span style="color:#000000;">></span><span style="color:#000000;">
               </span><span style="color:#000000;"><</span><span style="color:#000000;">ItemTemplate</span><span style="color:#000000;">></span><span style="color:#000000;">
                   </span><span style="color:#000000;"><</span><span style="color:#000000;">asp:Label Text</span><span style="color:#000000;">=</span><span style="color:#800000;">'</span><span style="color:#800000;"><%# DataBinder.Eval(Container.DataItem, "Name") %></span><span style="color:#800000;">'</span><span style="color:#000000;"> runat</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">server</span><span style="color:#800000;">"</span><span style="color:#000000;"> ID</span><span style="color:#000000;">=</span><span style="color:#800000;">"</span><span style="color:#800000;">lblName</span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">/></span><span style="color:#000000;">
               </span><span style="color:#000000;"></</span><span style="color:#000000;">ItemTemplate</span><span style="color:#000000;">></span><span style="color:#000000;">
           </span><span style="color:#000000;"></</span><span style="color:#000000;">asp:TemplateField</span><span style="color:#000000;">></span><span style="color:#000000;">
       </span><span style="color:#000000;"></</span><span style="color:#000000;">Columns</span><span style="color:#000000;">></span><span style="color:#000000;">
    </span><span style="color:#000000;"></</span><span style="color:#000000;">asp:GridView</span><span style="color:#000000;">></span><span style="color:#000000;">
    </span>





The code-behind deals with the sorting events and calls populate :






  
    
    <span style="color:#0000FF;">protected</span><span style="color:#000000;"> </span><span style="color:#0000FF;">void</span><span style="color:#000000;"> gvList_Sorting(</span><span style="color:#0000FF;">object</span><span style="color:#000000;"> sender, GridViewSortEventArgs e)
    {
       Populate(e.SortExpression </span><span style="color:#000000;">+</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;"> </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">+</span><span style="color:#000000;"> GetSortDirection(e.SortExpression));
    }
    
    </span><span style="color:#0000FF;">private</span><span style="color:#000000;"> </span><span style="color:#0000FF;">string</span><span style="color:#000000;"> GetSortDirection(</span><span style="color:#0000FF;">string</span><span style="color:#000000;"> column)
    {
    
       </span><span style="color:#008000;">//</span><span style="color:#008000;"> By default, set the sort direction to ascending.</span><span style="color:#008000;">
    </span><span style="color:#000000;">   </span><span style="color:#0000FF;">string</span><span style="color:#000000;"> sortDirection </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">ASC</span><span style="color:#800000;">"</span><span style="color:#000000;">;
    
       </span><span style="color:#008000;">//</span><span style="color:#008000;"> Retrieve the last column that was sorted.</span><span style="color:#008000;">
    </span><span style="color:#000000;">   </span><span style="color:#0000FF;">string</span><span style="color:#000000;"> sortExpression </span><span style="color:#000000;">=</span><span style="color:#000000;"> ViewState[</span><span style="color:#800000;">"</span><span style="color:#800000;">SortExpression</span><span style="color:#800000;">"</span><span style="color:#000000;">] </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> </span><span style="color:#0000FF;">string</span><span style="color:#000000;">;
    
       </span><span style="color:#0000FF;">if</span><span style="color:#000000;"> (sortExpression </span><span style="color:#000000;">!=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">null</span><span style="color:#000000;">)
       {
           </span><span style="color:#008000;">//</span><span style="color:#008000;"> Check if the same column is being sorted.
           </span><span style="color:#008000;">//</span><span style="color:#008000;"> Otherwise, the default value can be returned.</span><span style="color:#008000;">
    </span><span style="color:#000000;">       </span><span style="color:#0000FF;">if</span><span style="color:#000000;"> (sortExpression </span><span style="color:#000000;">==</span><span style="color:#000000;"> column)
           {
               </span><span style="color:#0000FF;">string</span><span style="color:#000000;"> lastDirection </span><span style="color:#000000;">=</span><span style="color:#000000;"> ViewState[</span><span style="color:#800000;">"</span><span style="color:#800000;">SortDirection</span><span style="color:#800000;">"</span><span style="color:#000000;">] </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> </span><span style="color:#0000FF;">string</span><span style="color:#000000;">;
               </span><span style="color:#0000FF;">if</span><span style="color:#000000;"> ((lastDirection </span><span style="color:#000000;">!=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">null</span><span style="color:#000000;">) </span><span style="color:#000000;">&&</span><span style="color:#000000;"> (lastDirection </span><span style="color:#000000;">==</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">ASC</span><span style="color:#800000;">"</span><span style="color:#000000;">))
               {
                   sortDirection </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">DESC</span><span style="color:#800000;">"</span><span style="color:#000000;">;
               }
           }
       }
    
       </span><span style="color:#008000;">//</span><span style="color:#008000;"> Save new values in ViewState.</span><span style="color:#008000;">
    </span><span style="color:#000000;">   ViewState[</span><span style="color:#800000;">"</span><span style="color:#800000;">SortDirection</span><span style="color:#800000;">"</span><span style="color:#000000;">] </span><span style="color:#000000;">=</span><span style="color:#000000;"> sortDirection;
       ViewState[</span><span style="color:#800000;">"</span><span style="color:#800000;">SortExpression</span><span style="color:#800000;">"</span><span style="color:#000000;">] </span><span style="color:#000000;">=</span><span style="color:#000000;"> column;
    
       </span><span style="color:#0000FF;">return</span><span style="color:#000000;"> sortDirection;
    }
    </span>








What this all boils down to is the _OrderByExpression()_ extension method called in the _Populate()_ method above:




    
    <span style="color:#000000;">   </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> </span><span style="color:#0000FF;">static</span><span style="color:#000000;"> IEnumerable</span><span style="color:#000000;"><</span><span style="color:#000000;">TSource</span><span style="color:#000000;">></span><span style="color:#000000;"> OrderByExpression</span><span style="color:#000000;"><</span><span style="color:#000000;">TSource</span><span style="color:#000000;">></span><span style="color:#000000;">(</span><span style="color:#0000FF;">this</span><span style="color:#000000;"> IEnumerable</span><span style="color:#000000;"><</span><span style="color:#000000;">TSource</span><span style="color:#000000;">></span><span style="color:#000000;"> data, OrderByExpression expression)
       {
           </span><span style="color:#0000FF;">string</span><span style="color:#000000;"> sortOrderMethod </span><span style="color:#000000;">=</span><span style="color:#000000;"> (expression.SortOrder </span><span style="color:#000000;">==</span><span style="color:#000000;"> SortOrderEnum.Ascending) </span><span style="color:#000000;">?</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">OrderBy</span><span style="color:#800000;">"</span><span style="color:#000000;"> : </span><span style="color:#800000;">"</span><span style="color:#800000;">OrderByDescending</span><span style="color:#800000;">"</span><span style="color:#000000;">;
    
           </span><span style="color:#008000;">//</span><span style="color:#008000;"> data.OrderBy(o => o.propertyname);   </span><span style="color:#008000;">
    </span><span style="color:#000000;">       var dataAsQueryable </span><span style="color:#000000;">=</span><span style="color:#000000;"> data.AsQueryable</span><span style="color:#000000;"><</span><span style="color:#000000;">TSource</span><span style="color:#000000;">></span><span style="color:#000000;">();
           ParameterExpression lambdaParameter </span><span style="color:#000000;">=</span><span style="color:#000000;"> Expression.Parameter(</span><span style="color:#0000FF;">typeof</span><span style="color:#000000;">(TSource), </span><span style="color:#800000;">"</span><span style="color:#800000;">o</span><span style="color:#800000;">"</span><span style="color:#000000;">);
           MemberExpression member </span><span style="color:#000000;">=</span><span style="color:#000000;"> Expression.PropertyOrField(lambdaParameter, expression.SortProperties[</span><span style="color:#800080;">0</span><span style="color:#000000;">]);
           LambdaExpression lambda </span><span style="color:#000000;">=</span><span style="color:#000000;"> Expression.Lambda(member, lambdaParameter);
           Type[] argumentTypes </span><span style="color:#000000;">=</span><span style="color:#000000;"> { dataAsQueryable.ElementType, lambda.Body.Type };
           MethodCallExpression orderBy </span><span style="color:#000000;">=</span><span style="color:#000000;"> Expression.Call(</span><span style="color:#0000FF;">typeof</span><span style="color:#000000;">(Queryable), sortOrderMethod, argumentTypes, dataAsQueryable.Expression, lambda);
           </span><span style="color:#0000FF;">return</span><span style="color:#000000;"> dataAsQueryable.Provider.CreateQuery</span><span style="color:#000000;"><</span><span style="color:#000000;">TSource</span><span style="color:#000000;">></span><span style="color:#000000;">(orderBy);
       }</span>





I have to say thanks to Joseph Albahari and Ben Albahari of [LinqPad](www.linqpad.net) fame since I found this solution to my problems (after getting within so close through my own efforts whilst our internet connection was down in the office!!) in the samples that came with LinqPad.





The code above creates a dynamic Linq expression then executes it through the queryables provider – although this only occurs once the whole linq expression is enumerated.





The last few modifications I need to make require the order by to cope with multiple properties (The _OrderByExpression.SortProperties_ class already supports multiple properties but I’m only taking the first at the moment) and I need to ensure that the query always deferred since composable against a database – this is important since I’ve yet to consider paging in the GridView and I want to make sure that specifiying _.Skip(n).Take(m)_ can be applied after the ordering but the Linq expression be composed as SQL and executed against the database in one go otherwise I’ll be returning all the rows to the application layer before extracting just the required rows for the current page.
