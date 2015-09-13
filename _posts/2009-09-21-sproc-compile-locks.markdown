---
author: philiphendry
comments: true
date: 2009-09-21 19:28:48+00:00
layout: post
slug: sproc-compile-locks
title: Sproc Compile Locks
wordpress_id: 289
tags:
- sql-server
- tip
---

I came across an [article](http://support.microsoft.com/?id=263889) on Microsofts support site which has raised a point which I’d never considered before. It basically says that if the user that executes a stored procedure is not the owner of the procedure then locks are acquired during the process to find a cached plan that may lead to blocking. It also states this could be avoided by fully qualifying the sproc name (e.g. dbo.mysproc) when calling it to avoid the extra lookups. This has been a problem for me in the past since the projects I’ve worked on had been setup with an account running as dbowner, however, if I was setting up a database from scratch I’d set up a specific user(s) for accessing the database and give it just the permissions it requires (sproc execute permissions for example.) I think, therefore, this would fall into these problem. However, I would also make use of the schema prefix (e.g. Sales.Order table) rather than leaving it as dbo and therefore avoid this problem altogether.
