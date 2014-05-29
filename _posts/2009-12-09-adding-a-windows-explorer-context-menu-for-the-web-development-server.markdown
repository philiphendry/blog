---
author: philiphendry
comments: true
date: 2009-12-09 16:17:37+00:00
layout: post
slug: adding-a-windows-explorer-context-menu-for-the-web-development-server
title: Adding a Windows Explorer Context Menu for the Web Development Server
wordpress_id: 299
categories:
- Tip
- Windows
- Windows 7
---

I wanted to be able to launch a single folder – the results of an ASP.NET WebForms build - in a Web Development Server without having to run up Visual Studio. The result looks like this :

 

[![image](http://philiphendry.files.wordpress.com/2009/12/image_thumb.png)](http://philiphendry.files.wordpress.com/2009/12/image.png)

 

And the solution… add the following to the registry :

 
    
    <span style="color:#000000;">Windows Registry Editor Version </span><span style="color:#000000;">5.00</span><span style="color:#000000;">
    
    </span><span style="color:#800000;font-weight:bold;">[</span><span style="color:#800000;">HKEY_CLASSES_ROOT\Directory\shell\webdev</span><span style="color:#800000;font-weight:bold;">]</span><span style="color:#000000;">
    @</span><span style="color:#000000;">=</span><span style="color:#000000;">"</span><span style="color:#000000;">Launch in WebDev Server</span><span style="color:#000000;">"</span><span style="color:#000000;">
    
    </span><span style="color:#800000;font-weight:bold;">[</span><span style="color:#800000;">HKEY_CLASSES_ROOT\Directory\shell\webdev\command</span><span style="color:#800000;font-weight:bold;">]</span><span style="color:#000000;">
    @</span><span style="color:#000000;">=</span><span style="color:#000000;">"</span><span style="color:#000000;">\</span><span style="color:#000000;">"</span><span style="color:#000000;">c:\\windows\\microsoft.net\\framework\\v2</span><span style="color:#000000;">.0.50727</span><span style="color:#000000;">\\WebDev.WebServer.exe\</span><span style="color:#000000;">"</span><span style="color:#000000;"> /port:54321 /path:\</span><span style="color:#000000;">"</span><span style="color:#000000;">%V\</span><span style="color:#000000;">""</span><span style="color:#000000;">
    
    </span>





I would like to change that so it uses %windir% instead of C:\Windows but I must have got the syntax wrong because Explorer wouldn’t recognise the command. I’d also like to add an option for .aspx files that launches the parent folder in WebDev then launches an explorer window for the selected aspx file.





One last thing… this isn’t launching the server with any elevated permissions. If that’s required then you’ll need a tool such as [Wintellects Elevate](http://www.wintellect.com/CS/files/folders/sample_files/entry3385.aspx) command which can be downloaded with full C# source code.
