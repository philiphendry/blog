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

 
```
update code set serialNumber = floor(rand() * 30000000000)
```


Unfortunately this set the serialNumber to exactly the same value since RAND() isn’t being re-executed for each row. I was in a bit of a hurry so instead found the following on google :
    
```
update code set serialNumber = abs(cast(cast(newid() as binary(4)) as int)) + 30000000000
```

Newid() is executed for each row and in this example it’s being used to created (aka ‘bodge’) an int. An interesting solution and did the trick for me.
