---
author: philiphendry
comments: true
date: 2009-09-02 10:55:10+00:00
layout: post
slug: selecting-a-result-set-from-a-stored-procedure-in-sql-server
title: Selecting a result set from a stored procedure in SQL Server
wordpress_id: 282
tags:
- sql-server
- t-sql
- tip
---

Selecting data from a stored procedure in SQL Server is already a documented feature and here’s an example:

 
    
```
insert into #systables exec sp_executesql N'select * from Northwind.sys.tables'
```




…problem is this example doesn’t run without first creating the temp table and therefore knowing all the column definitions. When I’m running quick queries this isn’t exactly convenient. I’ve seen blogs posts using a linked server but there’s another way :



```
select * into #systables 
from openrowset(
   'sqlncli', 
   'server=.;trusted_connection=Yes', 
   'sp_executesql N''select * from Northwind.sys.tables'''
)
```
    

I wouldn’t necessarily use this as a day-to-day process on a production environment but for administration or scripting installations I think it fits the bill.


