---
author: philiphendry
comments: true
date: 2010-08-09 14:26:04+00:00
layout: post
slug: manually-converting-a-visual-studio-project-to-include-asp-net-mvc-context-menus
title: Manually converting a Visual studio project to include asp.net mvc Context
  menus
wordpress_id: 311
categories:
- ASP.NET MVC
- Visual Studio
---

I’ve got a Visual Studio project that includes ASP.NET MVC controllers, models and view utilities but because it was not a Web Application I wasn’t getting any of the context menus for adding MVC elements. For example, ‘Controller’ was missing from the Add menu :

 

[![image](/assets/2010/08/image_thumb.png)](/assets/2010/08/image.png)

 

To fix this I unloaded the project, edited the csproj and added the following line :

 
    
```
<PropertyGroup>
    ...
    <ProjectTypeGuids>{603c0e0b-db56-11dc-be95-000d561079b0};{fae04ec0-301f-11d3-bf4b-00c04f79efbc}</ProjectTypeGuids>
    ...
</PropertyGroup>
```





Using a [list of known project type guids](http://www.mztools.com/Articles/2008/MZ2008017.aspx) I could identify the second guid which is Windows (C#) whilst the first I found by googling and checking a test ASP.NET MVC application I created with Visual Studio.
