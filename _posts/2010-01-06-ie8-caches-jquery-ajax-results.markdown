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

 
    
    <span style="color:#000000;">$.getJSON(_config.getUrl, </span><span style="color:#0000FF;">null</span><span style="color:#000000;">, </span><span style="color:#0000FF;">function</span><span style="color:#000000;">(data) {
        _shiftPattern </span><span style="color:#000000;">=</span><span style="color:#000000;"> data;
        renderModel();
    });
    </span>





However, my server code was only being called on the first visit to the page and on subsequent attempts the browser seemed to be fetching cached results. Since my server side code is ASP.NET MVC the solution to this is very simple indeed and simply involves decorating the action method with a OutputCache attribute :




    
    <span style="color:#000000;">[OutputCache(Duration </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800080;">0</span><span style="color:#000000;">, VaryByParam </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">None</span><span style="color:#800000;">"</span><span style="color:#000000;">)]
    </span><span style="color:#0000FF;">public</span><span style="color:#000000;"> ActionResult GetJson(</span><span style="color:#0000FF;">int</span><span style="color:#000000;"> id)
    {
        </span><span style="color:#0000FF;">return</span><span style="color:#000000;"> Content(
            JsonHelper.Serialize(ShiftPatternBL.Get(id)), 
            </span><span style="color:#800000;">"</span><span style="color:#800000;">application/json; charset=utf-8</span><span style="color:#800000;">"</span><span style="color:#000000;">
            );
    }
    </span>





ASP.NET MVC is proving very elegant indeed :)
