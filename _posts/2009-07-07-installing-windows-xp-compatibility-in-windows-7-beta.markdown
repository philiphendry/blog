---
author: philiphendry
comments: true
date: 2009-07-07 19:08:13+00:00
layout: post
slug: installing-windows-xp-compatibility-in-windows-7-beta
title: Installing Windows XP Compatibility in Windows 7 Beta
wordpress_id: 230
categories:
- Windows 7
---

There’s a specific [Microsoft page](http://www.microsoft.com/windows/virtual-pc/download.aspx) where you can download the beta of Virtual PC and Windows XP Mode which will provide you a full virtualised Windows XP environment in Windows 7 providing the best compatibility ever!

 

However, I didn’t think my Dell Inspiron 6400 was that old but it appears it doesn’t support Intel VT (Virtualization Technology) and the Virtual PC Beta annoyingly requires it. I used a tool called [securable](http://www.grc.com/securable.htm) which told me pretty clearly where I stood :

 

[![image](http://philiphendry.files.wordpress.com/2009/07/image_thumb.png)](http://philiphendry.files.wordpress.com/2009/07/image.png)

 

As a side note, the [site](http://www.grc.com) that offered the securable tool also provides the neat tool called Shields Up! which can test the effectiveness of your internet security.

 

I managed to work around the issue of no virtualisation by running the Windows XP VHD from Virtual PC 2007 SP1 – however, the Virtual PC beta seemed to break my installation of 2007 so I had to uninstall both and re-install 2007.
