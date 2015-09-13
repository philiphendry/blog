---
author: Philip Hendry
title: Adding a generic catch-all route in ASP.NET MVC for AngularJS
date: 2014-06-08 07:54:00+00:00
comments: true
layout: post
tags:
- angularjs
- asp-net-mvc
---
I'm using ASP.NET MVC to host my AngularJS and inside the ASP.NET MVC site I'm also hosting WebAPI controllers to server JSON to the AngularJS code. This means I have three routing tables! However, the routing table for ASP.NET MVC just needs to forward everything to the browser and let the Angular routing table kick in (so pressing F5 in my Angular app can actually mean *refresh this page*.)

I haven't found official documentation for this syntax but it seems to work. This code should be added to the ASP.NET MVC routing table where the key to this technique is `{*url}`:

```
routes.MapRoute(
    name: "Default",
    url: "{*url}",
    defaults: new
    {
        controller = "Default",
        action = "Default"
    });
```



