---
author: philiphendry
comments: true
date: 2009-09-02 10:55:10+00:00
layout: post
slug: selecting-a-result-set-from-a-stored-procedure-in-sql-server
title: Selecting a result set from a stored procedure in SQL Server
wordpress_id: 282
categories:
- SQL Server
- T-SQL
- Tip
---

Selecting data from a stored procedure in SQL Server is already a documented feature and here’s an example:

 
    
    <span style="color:blue;">insert into </span>#systables <span style="color:blue;">exec </span><span style="color:maroon;">sp_executesql </span>N<span style="color:red;">'select * from Northwind.sys.tables'</span>





…problem is this example doesn’t run without first creating the temp table and therefore knowing all the column definitions. When I’m running quick queries this isn’t exactly convenient. I’ve seen blogs posts using a linked server but there’s another way :




    
    <span style="color:blue;">select </span><span style="color:gray;">* </span><span style="color:blue;">into </span>#systables 
    <span style="color:blue;">from openrowset</span><span style="color:gray;">(
       </span><span style="color:red;">'sqlncli'</span><span style="color:gray;">, 
       </span><span style="color:red;">'server=.;trusted_connection=Yes'</span><span style="color:gray;">, 
       </span><span style="color:red;">'sp_executesql N''select * from Northwind.sys.tables'''
    </span><span style="color:gray;">)</span>


[](http://11011.net/software/vspaste)I wouldn’t necessarily use this as a day-to-day process on a production environment but for administration or scripting installations I think it fits the bill.


