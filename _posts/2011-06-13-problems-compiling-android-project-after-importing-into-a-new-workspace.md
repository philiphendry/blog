---
author: philiphendry
comments: true
date: 2011-06-13 18:34:49+00:00
layout: post
slug: problems-compiling-android-project-after-importing-into-a-new-workspace
title: Problems Compiling Android Project After Importing Into A New Workspace
wordpress_id: 360
tags:
- android
- eclipse
---

A couple of times now I’ve had compilation problems trying to compile an Android project after having just imported it into a new Workspace – and usually it seems to concern references to system packages. The solution seems to be:

 

  
  * Checking Windows –> Preferences –> Android –> SDK location which has blanked out. By resetting the path to the SDK then the SDK Targets re-appear.
   
  * Selecting Package Explorer –> <project> –> Android Tools –> Fix Project Properties. I can’t remember the exact reason this helped but it certainly did when loading source code that had accompanied a book.
