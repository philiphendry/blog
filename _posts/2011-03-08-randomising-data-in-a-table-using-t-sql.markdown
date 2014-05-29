---
author: philiphendry
comments: true
date: 2011-03-08 08:48:08+00:00
layout: post
slug: randomising-data-in-a-table-using-t-sql
title: Randomising data in a table using T-sql
wordpress_id: 349
categories:
- SQL Server
- T-SQL
---

I needed to obfuscate some data for a demo server but rather than re-invent all the data I simply wanted to randomly swap data so associations could not be made between a store and pay rates for example. After a bit of head scratching I came to the conclusion that I simply needed to be able to correlate one set of data with a random set. The simplest way I thought of was to use a row_number() to correlate one list of data with another where one set of row numbers were ordered by a primary key whilst the other used a little trick of ordering by newid() which is recalculated for each row and therefore randomly distributes the data. Here’s the code :

 
    
    <span style="color:#0000FF;">select</span><span style="color:#000000;"> </span><span style="color:#808080;">*</span><span style="color:#000000;">
    </span><span style="color:#008080;">--</span><span style="color:#008080;">update s1 set s1.StoreName = s2.StoreName, s1.StoreAddress1 = s2.StoreAddress1, s1.County = s2.County, s1.Town = s2.Town, s1.PostCode = s2.PostCode, s1.Telephone = s2.Telephone</span><span style="color:#008080;">
    </span><span style="color:#0000FF;">from</span><span style="color:#000000;"> (</span><span style="color:#0000FF;">select</span><span style="color:#000000;"> row_number() </span><span style="color:#0000FF;">over</span><span style="color:#000000;">(</span><span style="color:#0000FF;">order</span><span style="color:#000000;"> </span><span style="color:#0000FF;">by</span><span style="color:#000000;"> StoreID) </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> RowNumber, StoreId, StoreName, StoreAddress1, County, Town, PostCode, Telephone </span><span style="color:#0000FF;">from</span><span style="color:#000000;"> Stores s1) </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> s1 
    </span><span style="color:#808080;">join</span><span style="color:#000000;"> (</span><span style="color:#0000FF;">select</span><span style="color:#000000;"> row_number() </span><span style="color:#0000FF;">over</span><span style="color:#000000;">(</span><span style="color:#0000FF;">order</span><span style="color:#000000;"> </span><span style="color:#0000FF;">by</span><span style="color:#000000;"> </span><span style="color:#FF00FF;">newid</span><span style="color:#000000;">()) </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> RowNumber, StoreId, StoreName, StoreAddress1, County, Town, PostCode, Telephone </span><span style="color:#0000FF;">from</span><span style="color:#000000;"> Stores s2) </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> s2 </span><span style="color:#0000FF;">on</span><span style="color:#000000;"> s1.RowNumber </span><span style="color:#808080;">=</span><span style="color:#000000;"> s2.RowNumber 
    </span>
