---
author: philiphendry
comments: true
date: 2009-08-25 09:49:24+00:00
layout: post
slug: strongly-typing-enumeration-parsing
title: Strongly Typing Enumeration Parsing
wordpress_id: 267
categories:
- .Net
- Code
---

Given this enum :

 

[![image](http://philiphendry.files.wordpress.com/2009/08/image_thumb3.png)](http://philiphendry.files.wordpress.com/2009/08/image3.png)

 

Instead of this code :[![image](http://philiphendry.files.wordpress.com/2009/08/image_thumb4.png)](http://philiphendry.files.wordpress.com/2009/08/image4.png)

 

I would rather see this :

 

[![image](http://philiphendry.files.wordpress.com/2009/08/image_thumb5.png)](http://philiphendry.files.wordpress.com/2009/08/image5.png)

 

I can’t add an extension to the Enum system class but I can create a new generic Enum class which strongly types the results :

 

[![image](http://philiphendry.files.wordpress.com/2009/08/image_thumb6.png)](http://philiphendry.files.wordpress.com/2009/08/image6.png)
