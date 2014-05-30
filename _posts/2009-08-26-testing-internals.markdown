---
author: philiphendry
comments: true
date: 2009-08-26 08:18:16+00:00
layout: post
slug: testing-internals
title: Testing Internals
wordpress_id: 270
categories:
- .Net
- Code
- Testing
---

There’s a very handy attribute called [InternalsVisibleTo](http://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.internalsvisibletoattribute.aspx) that can be used in a class to make internally declared members accessible to a specific assembly. This probably isn’t wise for most situations but it’s very handy for testing internals from a separate test assembly. It’s also handy to provide a parameterised constructor that can accept different dependencies whilst the default constructor is hard-wired to the standard dependencies – this seems like an easy way to add DI for the purposes of testing to existing applications without disturbing the app structure too much or introducing a confusing DI factory. 

 

It’s a bit of a code smell adding code simply for testing purposes and in particular following different paths through the code during testing whilst production code follows another path (default constructor in production, parameterised in test) however, I think it’s a reasonable compromise to take if it means adding valuable tests without too much extra work!

 

[![image](/assets/2009/08/image_thumb7.png)](/assets/2009/08/image7.png)
