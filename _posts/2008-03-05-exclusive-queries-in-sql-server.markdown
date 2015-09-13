---
author: philiphendry
comments: true
date: 2008-03-05 16:58:17+00:00
layout: post
slug: exclusive-queries-in-sql-server
title: Exclusive Queries in SQL Server
wordpress_id: 31
tags:
- sql-server
---

We had a major problem last week when someone ran a query manually that was only ever designed to be run once under controlled conditions. To put this right we decided to add some protection to the stored procedure that would prevent it from running twice in a row however, because we don't employ a transaction around the whole stored procedure (it does too much for that) we couldn't use the simplest option [sp_getapplock](http://technet.microsoft.com/en-us/library/ms189823.aspx) which requires a transaction.

Although a little nasty, a simple and effective solution was used. A temporary table was created by the stored procedure is that temporary table didn't already exist in tempdb.sys.objects. When the stored procedure ends the temporary table is automatically removed (although if the stored procedure raises an exception there's a bug in SQL Server 2005, fixed in SP2, that leaves the temporary table there!!)
