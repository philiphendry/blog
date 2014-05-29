---
author: philiphendry
comments: true
date: 2008-06-08 17:32:45+00:00
layout: post
slug: failed-to-map-the-path
title: Failed to map the path '/'.
wordpress_id: 81
categories:
- .Net
- Dev Problem
- Visual Studio
---

 

I was trying to run a simple [MVC#](http://www.mvcsharp.org) example to see how it all hung together and whilst I found the WinForms example up and running within a couple of minutes the WebForms was a little harder and was failing with ‘Failed to map the path ‘/’.’

 

A few minutes consultation with support (aka Google) didn’t seem to suggested anything. But a moment later (and a slap of the forehead) I’d realised the problem – I was running Visual Studio as myself instead of Administrator and the default web server was struggling.

 

If someone out there knows which folder needs the appropriate permissions then let me know but for the moment I’ll just run as Administrator :(
