---
author: philiphendry
comments: true
date: 2009-12-22 21:17:34+00:00
layout: post
slug: sending-and-receiving-json-between-jquery-and-asp-net-mvc-using-ajax
title: Sending and Receiving JSON between jQuery and ASP.NET MVC using Ajax.
wordpress_id: 300
categories:
- ASP.NET MVC
- jQuery
---

It’s taken me a while to get this working without any problems so it’s worth jotting down how it all works. I don’t think I was trying to do anything particularly difficult – just receive Json from an ASP.NET MVC application using jQuery then submitted but the APIs had to be coerced into just the correct way to get it functional.

 

### Receiving Json from ASP.NET MVC using jQuery

 

First up, here’s the Json format I’m trying to send and receive :

 
    
```
var _shiftPattern = {
  'employee': {'name' : 'Jason Wright', 'number': 1234, 'contractedHours': 39.0 },
  'stepSize': 0.5,
  'days': [
      { 'day': 'Sunday',    'pattern': ['', '', '', '', '', '', '', 'p', 'b', 'w', 'w', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', 'p', 'p', 'p', 'p', 'p', 'b', 'p', 'p', 'p', '', '', '', '', '', '', '', '', '', '', '' ] },
      { 'day': 'Monday',    'pattern': ['', '', '', '', '', '', '', 'p', 'b', 'w', 'p', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', 'p', 'p', 'p', 'p', 'p', 'b', 'p', 'p', 'p', '', '', '', '', '', '', '', '', '', '', '' ] },
      { 'day': 'Tuesday',   'pattern': ['', '', '', '', '', '', '', 'p', 'b', 'w', 'b', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', 'p', 'p', 'p', 'p', 'p', 'b', 'p', 'p', 'p', '', '', '', '', '', '', '', '', '', '', '' ] },
      { 'day': 'Wednesday', 'pattern': ['', '', '', '', '', '', '', 'p', 'b', 'w', 'w', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', 'w', 'w', 'p', 'p', 'p', 'b', 'p', 'p', 'p', '', '', '', '', '', '', '', '', '', '', '' ] },
      { 'day': 'Thursday',  'pattern': ['', '', '', '', '', '', '', 'p', 'b', 'w', 'p', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', 'w', 'w', 'p', 'p', 'p', 'b', 'p', 'p', 'p', '', '', '', '', '', '', '', '', '', '', '' ] },
      { 'day': 'Friday',    'pattern': ['', '', '', '', '', '', '', 'p', 'b', 'w', 'b', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', 'w', 'w', 'p', 'p', 'p', 'b', 'p', 'p', 'p', '', '', '', '', '', '', '', '', '', '', '' ] },
      { 'day': 'Saturday',  'pattern': ['', '', '', '', '', '', '', 'p', 'b', 'w', 'w', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', 'w', 'w', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '', '' ] }
  ]
};
```





Note that I’ve opted to maintain Javascript standards by using camel casing – that caused a bit of trouble and I’ll show the problem and work-around later.





The jQuery to fetch this Json is trivial :




    
```
$(document).ready(function() {
  $.getJSON("/willingtowork/shiftpattern/1234", null, function(data) {
      _shiftPattern = data;
      renderModel();
  });
});
```





…and ASP.NET MVC controller action looks like this :




    
```
public ActionResult ShiftPattern(int id)
{
    return Content(JsonHelper.Serialize(
        new WillingToWork
        {
            Employee = new Employee { Name = "Jason Wright", Number = id, ContractedHours = 39.0f },
            StepSize = 0.5f,
            Days = new List<Day> {
                new Day { DayName = "Sun", Pattern = new List<string> {"", "", "", "", "", "", "", "p", "p", "b", "p", "p", "p", "p", "w", "w", "", "", "", "", "", "", "", "", "p", "p", "p", "p", "p", "p", "p", "w", "w", "w", "w", "w", "", "", "", "", "", "", "", "", "", "", "", "" }},
                new Day { DayName = "Mon", Pattern = new List<string> {"", "", "", "", "", "", "", "p", "p", "b", "p", "p", "p", "p", "w", "w", "", "", "", "", "", "", "", "", "p", "p", "p", "p", "p", "p", "p", "b", "p", "p", "p", "p", "w", "w", "w", "", "", "", "", "", "", "", "", "" }},
                new Day { DayName = "Tue", Pattern = new List<string> {"", "", "", "", "", "", "", "p", "p", "b", "p", "p", "p", "p", "w", "w", "", "", "", "", "", "", "", "", "p", "p", "p", "p", "p", "p", "p", "b", "p", "p", "p", "p", "w", "w", "w", "", "", "", "", "", "", "", "", "" }},
                new Day { DayName = "Wed", Pattern = new List<string> {"", "", "", "", "", "", "", "p", "p", "b", "p", "p", "p", "p", "w", "w", "", "", "", "", "", "", "", "", "p", "p", "p", "p", "p", "p", "p", "b", "p", "p", "p", "p", "w", "w", "w", "", "", "", "", "", "", "", "", "" }},
                new Day { DayName = "Thu", Pattern = new List<string> {"", "", "", "", "", "", "", "p", "p", "b", "p", "p", "p", "p", "w", "w", "", "", "", "", "", "", "", "", "p", "p", "p", "p", "p", "p", "p", "b", "p", "p", "p", "p", "w", "w", "w", "", "", "", "", "", "", "", "", "" }},
                new Day { DayName = "Fri", Pattern = new List<string> {"", "", "", "", "", "", "", "p", "p", "", "", "", "w", "w", "w", "w", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "" }},
                new Day { DayName = "Sat", Pattern = new List<string> {"", "", "", "", "", "", "", "", "", "", "", "", "w", "w", "w", "w", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "", "" }}
            }
        }
    ), "application/json; charset=utf-8");
}
```









In this test code I’ve hard-coded some object initialisations but the real issue for me here was originally choosing to use a JsonResult by calling Json() on the Mvc Controller Base but I’ve replaced it with a ContentResult and serialised the Json manually – more on that later.





The code below defines the data transfer objects I’m using to strongly type the Json in C#. Note I’ve implemented IExtensibleDataObject so that any additional data added to the Json in the javascript that I haven’t accounted for is ignored but still maintained if necessary (you may choose to remove this if you want to be strict but any extra data will cause an exception during deserialising.) I might also implement all properties as virtual to enable mocking frameworks (an alternative to defining interfaces which would be a bit complicated for DTOs) but that’s not shown here.





The main point to notice with the DataContract below is the use of DataMemberAttribute and its ability to rename properties – this is how I’m maintaining Pascal casing in C# and Camel casing for the serialised Json.




    
```
[DataContract]
public class WillingToWork : IExtensibleDataObject
{
   [DataMember(Name="employee")] public Employee Employee { get; set; }
   [DataMember(Name="days")] public List<Day> Days { get; set; }
   [DataMember(Name="stepSize")] public float StepSize { get; set; }

   public virtual ExtensionDataObject ExtensionData { get; set; }
}

[DataContract]
public class Day : IExtensibleDataObject
{
   [DataMember(Name = "pattern")] public List<string> Pattern { get; set; }
   [DataMember(Name = "day")] public string DayName { get; set; }

   public virtual ExtensionDataObject ExtensionData { get; set; }
}

[DataContract]
public class Employee :IExtensibleDataObject
{
   [DataMember(Name="contractedHours")] public float ContractedHours { get; set; }
   [DataMember(Name="name")] public string Name { get; set; }
   [DataMember(Name="number")] public int Number { get; set; }

   public virtual ExtensionDataObject ExtensionData { get; set; }
}
```




… and here lies a problem. When I was using the ASP.NET MVC JsonResult, the Json simply would not serialise using the names I’d provided in the DataMember and instead used the property name.





I’d already seen how to use the DataContractJsonSerializer() to manually create test code which correctly used the DataMemberAttribute() name property so I was wondering why JsonResult() wasn’t doing the same. Taking a look with reflector showed the following code in the JsonResult():




    
```
public override void ExecuteResult(ControllerContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException("context");
    }
    HttpResponseBase response = context.HttpContext.Response;
    if (!string.IsNullOrEmpty(this.ContentType))
    {
        response.ContentType = this.ContentType;
    }
    else
    {
        response.ContentType = "application/json";
    }
    if (this.ContentEncoding != null)
    {
        response.ContentEncoding = this.ContentEncoding;
    }
    if (this.Data != null)
    {
        JavaScriptSerializer serializer = new JavaScriptSerializer();
        response.Write(serializer.Serialize(this.Data));
    }
}
```




The problem in this code is the use of the JavaScriptSerializer() which is now marked obsolete (although that seems to be questionable) and doesn’t handle the DataMemberAttribute(). Rather than try to work around this too cleverly I’ve used the ContentResult() instead passing a Json string serialized from a helper using the DataContractJsonSerializer() and making sure to pass the content type. Here’s the serializer helper (not exactly rocket science):






  
    
```
public class JsonHelper
{
   public static string Serialize<T>(T obj)
   {
       var serializer = new DataContractJsonSerializer(obj.GetType());
       var ms = new MemoryStream();
       serializer.WriteObject(ms, obj);
       return Encoding.Default.GetString(ms.ToArray());
   }
}
```







### Posting Json using jQuery back to an ASP.NET MVC Controller





The next step was finishing the loop and allowing the client to post back the Json data in order for it to be persisted. Here’s the jQuery in the client:




    
```
function saveModel() {
  $.ajax({
      url: "/willingtowork/save",
      type: "POST",
      data: JSON.stringify(_shiftPattern),
      dataType : "json",
      contentType: "application/json; charset=utf-8",
      success: function(data, textStatus) {
          alert(data.result);
      },
      error: function(request, textStatus, errorThrown) {
          alert(request.responseText);
      }
  });
}
```




And the controller action that receives this:




    
```
[JsonFilter(Param = "data", RootType = typeof(WillingToWork))]
public ActionResult Save(WillingToWork data)
{
  return Json(new {result = "Success" });
}
```





The key here is the JsonFilterAttribute()




    
```
public class JsonFilter : ActionFilterAttribute
{

   public string Param { get; set; }
   public Type RootType { get; set; }

   public override void OnActionExecuting(ActionExecutingContext filterContext)
   {
       if (!(filterContext.HttpContext.Request.ContentType ?? string.Empty).Contains("application/json")) return;
       filterContext.ActionParameters[Param] = JsonHelper.Deserialize<WillingToWork>(filterContext.HttpContext.Request.InputStream);
   }
}

public class JsonHelper
{
   public static T Deserialize<T>(Stream sr)
   {
       var serializer = new DataContractJsonSerializer(typeof (T));
       return (T) serializer.ReadObject(sr);
   }
}
```




I sourced the JsonFilter from an article online although I’ve lost the reference now but I’m very grateful to whoever it was!!! I’ve also renamed the filter itself and changed it to use a helper for the deserializing. However, that filter does turn a Json string into a typed object with very little plumbing.





And that’s it!! Phew, it took a while and in the end the code needed to complete it is fairly simple. At some point I’ll probably create a new ActionResult() that wraps up the serializing using DataContractJsonSerializer() so that it’s not necessary to remember to use a ContentResult(), serializer and content type!!! Yeuck!
