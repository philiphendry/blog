---
author: philiphendry
comments: true
date: 2008-09-05 08:11:55+00:00
layout: post
slug: generating-random-numbers-in-a-sql-update
title: Generating random numbers in a SQL Update
wordpress_id: 163
categories:
- SQL Server
- T-SQL
---

I wanted to fill a column in a table with random numbers so off the top of my head wrote the following sql :

 
    
    <span style="color:blue;">update </span>code <span style="color:blue;">set </span>serialNumber <span style="color:gray;">= </span><span style="color:magenta;">floor</span><span style="color:gray;">(</span><span style="color:magenta;">rand</span><span style="color:gray;">() * </span>30000000000<span style="color:gray;">)</span>


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)



Unfortunately this set the serialNumber to exactly the same value since RAND() isn’t being re-executed for each row. I was in a bit of a hurry so instead found the following on google :




    
    <span style="color:blue;">update </span>code <span style="color:blue;">set </span>serialNumber <span style="color:gray;">= </span><span style="color:magenta;">abs</span><span style="color:gray;">(</span><span style="color:magenta;">cast</span><span style="color:gray;">(</span><span style="color:magenta;">cast</span><span style="color:gray;">(</span><span style="color:magenta;">newid</span><span style="color:gray;">() </span><span style="color:blue;">as binary</span><span style="color:gray;">(</span>4<span style="color:gray;">)) </span><span style="color:blue;">as int</span><span style="color:gray;">)) + </span>30000000000


[](http://11011.net/software/vspaste)















Newid() is executed for each row and in this example it’s being used to created (aka ‘bodge’) an int. An interesting solution and did the trick for me.
