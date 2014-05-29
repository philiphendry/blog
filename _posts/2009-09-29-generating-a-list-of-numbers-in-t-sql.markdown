---
author: philiphendry
comments: true
date: 2009-09-29 08:33:06+00:00
layout: post
slug: generating-a-list-of-numbers-in-t-sql
title: Generating a list of numbers in T-SQL
wordpress_id: 293
categories:
- SQL Server
- T-SQL
- Tip
---

I wanted to create a list of numbers so I could cross join them to another set of data to generate some test data quickly. There are ways to generate numbers using a CTE (Common Table Expression) or if you’re in a real hurry you can make us of a fairly undocumented table called master..spt_values. Seeing as this table isn’t well documented don’t go using it for production code since it might change but for quick one-off tasks it seems fine.

 

The following SQL creates a list of numbers from 0 to 2047 :

 
    
    <span style="color:#0000FF;">select</span><span style="color:#000000;"> </span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">number</span><span style="color:#FF0000;">]</span><span style="color:#000000;">
    </span><span style="color:#0000FF;">from</span><span style="color:#000000;"> master..spt_values
    </span><span style="color:#0000FF;">where</span><span style="color:#000000;"> type </span><span style="color:#808080;">=</span><span style="color:#000000;"> </span><span style="color:#FF0000;">'</span><span style="color:#FF0000;">P</span><span style="color:#FF0000;">'</span>
