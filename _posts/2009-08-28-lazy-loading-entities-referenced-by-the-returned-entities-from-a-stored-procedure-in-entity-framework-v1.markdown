---
author: philiphendry
comments: true
date: 2009-08-28 13:52:39+00:00
layout: post
slug: lazy-loading-entities-referenced-by-the-returned-entities-from-a-stored-procedure-in-entity-framework-v1
title: Lazy Loading Entities Referenced By The Returned Entities from a Stored Procedure
  in Entity Framework V1
wordpress_id: 276
categories:
- Code
- Entity Framework
---

Phew, that’s the title out of the way!!

 

I had a simple plan – run some complex and optimised SQL and return a graph of entities back to the app using Entity Framework. First problem is it doesn’t support returning anything but a single entity from the stored procedure so that’s eager loading done for! Second problem is the entity has to exist in the model so creating a POCO is out of the question and I’m not so sure about creating arbitrary data transfer objects in the model either (EF V2 should help with this.)

 

So I resorted to running the sproc then lazy loading the child objects I need. What I needed to do though was traverse a couple of references and test a field to limit the returned rows. And here’s what I ended up with after much poking around :

 

[![image](http://philiphendry.files.wordpress.com/2009/08/image_thumb8.png)](http://philiphendry.files.wordpress.com/2009/08/image8.png)

 

There are two straight-forward lazy loads using .Load() but I’m only attaching entities for Child3 based on the results of the ‘inner query’ which uses .CreateSourceQuery() to create a queryable.
