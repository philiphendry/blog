---
author: philiphendry
comments: true
date: 2010-01-05 10:08:53+00:00
layout: post
slug: numbering-xml-elements-in-t-sql
title: Numbering XML Elements in T-SQL
wordpress_id: 305
categories:
- T-SQL
---

I had a problem where I wanted to convert a chunk of XML passed to a T-SQL stored procedure into a relational model but at the same time number the elements. I used row_number() but that required an order by clause and I actually wanted to maintain the original order of the elements and not apply any other ordering. Initially I tried adding _order by 1_ to try and fool it into apply ‘no order’ but that didn’t work, instead I replaced the 1 with rand(). Because rand() is not recomputed for each row it effectively orders by a single number which is the equivalent of _order by 1_. Here’s the code :

 
    
    <span style="color:#0000FF;">declare</span><span style="color:#000000;"> </span><span style="color:#008000;">@xml</span><span style="color:#000000;"> xml
    </span><span style="color:#0000FF;">set</span><span style="color:#000000;"> </span><span style="color:#008000;">@xml</span><span style="color:#000000;"> </span><span style="color:#808080;">=</span><span style="color:#000000;"> </span><span style="color:#FF0000;">'</span><span style="color:#FF0000;">
    <root>
        <item>first</item>
        <item>second</item>
        <item>third</item>
        <item>fourth</item>
    </root></span><span style="color:#FF0000;">'</span><span style="color:#000000;">
    
    </span><span style="color:#0000FF;">select</span><span style="color:#000000;"> 
        T.c.value(</span><span style="color:#FF0000;">'</span><span style="color:#FF0000;">.</span><span style="color:#FF0000;">'</span><span style="color:#000000;">, </span><span style="color:#FF0000;">'</span><span style="color:#FF0000;">nvarchar(25)</span><span style="color:#FF0000;">'</span><span style="color:#000000;">) </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> ItemName,
        row_number() </span><span style="color:#0000FF;">over</span><span style="color:#000000;"> (</span><span style="color:#0000FF;">order</span><span style="color:#000000;"> </span><span style="color:#0000FF;">by</span><span style="color:#000000;"> </span><span style="color:#FF00FF;">rand</span><span style="color:#000000;">()) </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> ItemIndex
    </span><span style="color:#0000FF;">from</span><span style="color:#000000;"> </span><span style="color:#008000;">@xml</span><span style="color:#000000;">.nodes(</span><span style="color:#FF0000;">'</span><span style="color:#FF0000;">/root/item</span><span style="color:#FF0000;">'</span><span style="color:#000000;">) </span><span style="color:#0000FF;">as</span><span style="color:#000000;"> T(c)
    </span>





Which returns :





[![image](http://philiphendry.files.wordpress.com/2010/01/image_thumb.png)](http://philiphendry.files.wordpress.com/2010/01/image.png)





The use of the rand() function is a little strange but the code below should highlight what it’s doing :




    
    <span style="color:#0000FF;">select</span><span style="color:#000000;"> </span><span style="color:#FF00FF;">rand</span><span style="color:#000000;">(), </span><span style="color:#FF00FF;">newid</span><span style="color:#000000;">(), checksum(</span><span style="color:#FF00FF;">newid</span><span style="color:#000000;">()) 
    </span><span style="color:#0000FF;">from</span><span style="color:#000000;"> master..spt_values 
    </span><span style="color:#0000FF;">where</span><span style="color:#000000;"> </span><span style="color:#FF0000;">[</span><span style="color:#FF0000;">type</span><span style="color:#FF0000;">]</span><span style="color:#000000;"> </span><span style="color:#808080;">=</span><span style="color:#000000;"> </span><span style="color:#FF0000;">'</span><span style="color:#FF0000;">P</span><span style="color:#FF0000;">'</span>













Which returns :





[![image](http://philiphendry.files.wordpress.com/2010/01/image_thumb1.png)](http://philiphendry.files.wordpress.com/2010/01/image1.png)





As you can see the rand() returns the same number for every row since it is computed at the start of the query execution. However, newid() is recomputed for every row and by wrapping it with checksum() you can also use it to calculate random numbers for every row.
