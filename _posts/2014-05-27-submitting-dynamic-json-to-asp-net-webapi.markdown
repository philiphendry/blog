---
author: philiphendry
comments: true
date: 2014-05-27 14:42:14+00:00
layout: post
slug: submitting-dynamic-json-to-asp-net-webapi
title: Submitting dynamic JSON to ASP.NET WebAPI
wordpress_id: 419
tags:
- ASP.NET
- WebAPI
---





Submitting dynamic JSON to ASP.NET WebAPI







I’m writing an AngularJS/ASP.NET application that’s generating a dynamic form from some JSON configuration, allows the user to input some data, then submits JSON data from that form back to an ASP.NET WebAPI controller. The problem I needed to solve was how to declare the model on the WebAPI controller action when the model isn’t actually know and I didn’t want to write Javascript to transform the JSON on the client before submitting.





The data I’m submitting from the AngularJS client looks like this:




    
```
{ "Firstname": "Mickey", "Surname": "Mouse" }
```





The problem here is I don’t have a model with _Firstname_ and _Surname_ defined on it since these fields were defined by the user of our system in configuration. The solution turns out to be quite elegant thanks to WebAPI conventions and .Net 4.0.




    
```
[RoutePrefix("api/form")]
public class WebApiController : ApiController
{
    private readonly DbContext _dbContext = new DbContext();

    [Route("")]
    public void Post([FromBody] dynamic formData)
    {
        var form = new Form
        {
            ChangedOn = DateTime.Now, 
            FieldValues = new Collection<FieldValue>()
        };
        foreach (var item in formData)
        {
            var itemName = item.Name as String;             
            var fieldDefinitionId = _dbContext.FieldDefinitions
                .Where(fd => fd.Name == itemName)
                .Select(fd => fd.Id).First(); 
            var field = new FieldValue
            {
                FieldDefinitionId = fieldDefinitionId, 
                Value = item.Value.Value
            }; 
            nomination.FieldValues.Add(field);
        } 
        _dbContext.Forms.Add(form); 
        _dbContext.SaveChanges();
    }
}
```





This isn’t the most efficient code but the key point to not is the _dynamic_ keyword used for the action parameter _formData_. I can then loop through the collection of items in this dynamic collection and access both the Name and Value.





<blockquote>
  
> 
> Written with [StackEdit](https://stackedit.io/).
> 
> 
</blockquote>



