---
author: philiphendry
comments: true
date: 2011-03-08 08:48:08+00:00
layout: post
slug: randomising-data-in-a-table-using-t-sql
title: Randomising data in a table using T-sql
wordpress_id: 349
tags:
- sql-server
- t-sql
---

I needed to obfuscate some data for a demo server but rather than re-invent all the data I simply wanted to randomly swap data so associations could not be made between a store and pay rates for example. After a bit of head scratching I came to the conclusion that I simply needed to be able to correlate one set of data with a random set. The simplest way I thought of was to use a row_number() to correlate one list of data with another where one set of row numbers were ordered by a primary key whilst the other used a little trick of ordering by newid() which is recalculated for each row and therefore randomly distributes the data. Here’s the code :

 
```
select * 
--update s1 set s1.StoreName = s2.StoreName, s1.StoreAddress1 = s2.StoreAddress1, s1.County = s2.County, s1.Town = s2.Town, s1.PostCode = s2.PostCode, s1.Telephone = s2.Telephone 
from (
	select row_number() over(order by StoreID) as RowNumber, StoreId, StoreName, StoreAddress1, County, Town, PostCode, Telephone from Stores s1) as s1  
	join (select row_number() over(order by newid()) as RowNumber, StoreId, StoreName, StoreAddress1, County, Town, PostCode, Telephone from Stores s2) as s2 
	on s1.RowNumber = s2.RowNumber 
)
```
