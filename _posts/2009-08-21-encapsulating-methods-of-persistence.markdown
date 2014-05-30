---
author: philiphendry
comments: true
date: 2009-08-21 20:09:45+00:00
layout: post
slug: encapsulating-methods-of-persistence
title: Encapsulating methods of persistence
wordpress_id: 257
categories:
- ASP.NET
- Code
- Design
---

Here’s a question. What’s wrong with this code :

[![image](/assets/2009/08/image_thumb1.png)](/assets/2009/08/image1.png)

To be honest, there’s nothing wrong with it. And most applications would be absolutely fine. However, I find there are things I don’t like :



	
  1. The need to type cast.

	
  2. The use of a string to index the session – if this code is repeated elsewhere only testing will discover whether one of them was typed incorrectly.

	
  3. The use of the Session object itself!!


That last point is probably my biggest reason for writing the code a differently. When UserAccess is fetched elsewhere in the code, each line will be hardcoded with the method of access. If I changed my mind and wanted to place the object elsewhere, in view state or configuration file for example, then I’d have more places to edit. So what would I replace it with?

[![image](/assets/2009/08/image_thumb2.png)](/assets/2009/08/image2.png)

Seems like a lot of effort but some of that could be reduced by using templates if you don’t want to type too much! I cache the object so no matter how many times I use the property I’m not hitting the Session object and casting.  I’ve also added the property as virtual so a subclass of the page could override the access and replace it with something entirely different – which could be very useful for wrapping the code behind for the page in a unit test and therefore removing any need for ASP.NET objects.

I’ve highlighted this fairly small idea because it highlights some important concepts including that of writing code that maintains the original intent (line 3 now doesn’t have to include any code that indicates where or how the user object is stored) and isolating persistence methods even within a class.
