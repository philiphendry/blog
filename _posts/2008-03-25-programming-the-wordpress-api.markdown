---
author: philiphendry
comments: true
date: 2008-03-25 21:41:45+00:00
layout: post
slug: programming-the-wordpress-api
title: Programming the WordPress API
wordpress_id: 58
categories:
- Dev Tools
---

I've started taking a quick look at the API to get access to my own blog stored in WordPress so that I can cache all the pages locally as a backup and an offline repository. So far here's all I've needed to get something working in .Net :

  * Information concerning WordPress's [XML-RPC support](http://codex.wordpress.org/XML-RPC_Support)
  * A reference for the [metaWebLog](http://www.xmlrpc.com/metaWeblogApi)
  * A [download](http://xml-rpc.net/) for a XML-RPC wrapper for .Net with a very useful [FAQ](http://www.xml-rpc.net/faq/xmlrpcnetfaq.html#1.10)
  * A Microsoft reference for accessing [Microsoft Live Spaces](http://msdn2.microsoft.com/en-us/library/bb259702.aspx)
  * And most importantly some [source code](http://msdn2.microsoft.com/en-us/library/aa905670.aspx) from MSDN that works with very few changes

I'm not sure where to go with this at the moment - I'm sure there must already be a tool for this so don't want to go through the effort of writing my own!
