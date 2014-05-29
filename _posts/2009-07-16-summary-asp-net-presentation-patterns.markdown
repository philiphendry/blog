---
author: philiphendry
comments: true
date: 2009-07-16 13:14:37+00:00
layout: post
slug: summary-asp-net-presentation-patterns
title: 'Summary : ASP.NET Presentation Patterns'
wordpress_id: 237
categories:
- .Net
- MSDN Article Summary
- Patterns
---

This is a summary of the MSDN Magazine article [ASP.NET Presentation Patterns](http://msdn.microsoft.com/en-us/magazine/dd252940.aspx) made for my own reference but posted on my blog for others to benefit from.

 

This article discusses the available patterns that separate presentation logic from the UI technology and platform. The initial description discusses how a crude structure can be used to provide this separation by having the ASP.NET code-behind create and call a controller. The controller is passed a reference to the originating page but accesses controls through an interface (since the controls themselves are private and the controller would end up very tightly couple to the page – although it still is.)

 

## The Original MVC Pattern

 

An **_autonomous view_** is defined as :

 

<blockquote>  
> 
> ‘_a class that displays content, maintains state information for the view, and incorporates the full logic to handle any user actions’_.
> 
> </blockquote>

 

This isn’t very testable and therefore _Separation of Concerns (SoC)_ is applied with the added benefit that navigational workflow becomes easier. The following diagram, lifted from the article, demonstrates how a ‘standard’ MVC has the View handle user actions but passes the request to the Controller which it has a direct dependency with. The Controller decides what actions should take place and does this against the Model with which it has a direct dependency. However, the View is an [Observer](http://msdn.microsoft.com/en-us/library/ms978753.aspx) of the Model and therefore is notified of changes so that it can have a direct dependency on the Model but the Model is oblivious of who or how it’s being used.

 

![](http://i.msdn.microsoft.com/dd252940.fig04(en-us).gif)

 

## Model2 : A Web Variation of MVC

 

MVC Model2 attempts to provide a model for web interfaces since the standard MVC pattern was originally developed for desktop applications. A Front Controller (implemented as a HTTP module) coordinates all requests from the web browser to the web application and is represented in the diagram as the URL Router. The other major difference is the View is no longer aware of the Model as is ‘composed’ by the Page Controller.

 

This is the model adopted by ASP.NET MVC and provides a very simple, efficient and fast framework.

 

![](http://i.msdn.microsoft.com/dd252940.fig05(en-us).gif)

 

## The MVP Pattern

 

The Observer role in the Standard MVC pattern is one key weakness since the view must manipulate the model into the format it requires for display and therefore isn’t as passive as it should be for testing purposes. 

 

The MVP pattern has the Presenter (previously the Controller) update the View from the Model and therefore provides a better separation between the triad of components. This means the presentation logic is now in the Presenter which, as a plain class, can be thoroughly unit tested. Compared to Model2, MVP formally defines a contract between the presenter and the view.

 

![](http://i.msdn.microsoft.com/dd252940.fig06(en-us).gif)

 

## See also

 

[The original MVC definition](http://st-www.cs.uiuc.edu/users/smarch/st-docs/mvc.html)

 

Microsoft take on [Implementing Model-View-Controller in ASP.NET](http://msdn.microsoft.com/en-us/library/ms998540.aspx)

 

Another MSDN Magazine article for developing the [MVP pattern in ASP.NET](http://msdn.microsoft.com/en-gb/magazine/cc188690.aspx)

 

The [Microsoft Web Client Software Factory](http://msdn.microsoft.com/en-us/library/bb264518.aspx) natively supports MVP in ASP.NET

 

 

A [Microsoft MVP Quickstart](http://msdn.microsoft.com/en-gb/library/cc304754.aspx)
