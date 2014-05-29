---
author: philiphendry
comments: true
date: 2010-10-28 09:07:33+00:00
layout: post
slug: dont-check-in-binaries-files-and-resharper-files-into-svn
title: Don’t check in binaries files and ReSharper files into svn!!!
wordpress_id: 340
categories:
- Tip
---

Just added ****.ReSharper** **\_ReSharper.** *\bin* *\obj* *.suo *.user** to the TortoiseSvn global ignore pattern so I don't go checking in resharper files or binary folders that aren't needed – not that I ever did this before but it had always been a job I did by hand during the initial check-in of a project.
