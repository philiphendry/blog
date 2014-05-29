---
author: philiphendry
comments: true
date: 2010-01-06 10:07:15+00:00
layout: post
slug: ie8-caches-jquery-ajax-results
title: IE8 Caches jQuery Ajax results
wordpress_id: 306
categories:
- ASP.NET MVC
- jQuery
---

In my client code I have a small chunk of javascript that fetches the data for my form asynchronously using Ajax :

 
    
```
$.getJSON(_config.getUrl, null, function(data) {
    _shiftPattern = data;
    renderModel();
});
```





However, my server code was only being called on the first visit to the page and on subsequent attempts the browser seemed to be fetching cached results. Since my server side code is ASP.NET MVC the solution to this is very simple indeed and simply involves decorating the action method with a OutputCache attribute :




    
```
[OutputCache(Duration = 0, VaryByParam = "None")]
public ActionResult GetJson(int id)
{
    return Content(
        JsonHelper.Serialize(ShiftPatternBL.Get(id)), 
        "application/json; charset=utf-8"
        );
}
```




ASP.NET MVC is proving very elegant indeed :)
