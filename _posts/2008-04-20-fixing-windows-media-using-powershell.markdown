---
author: philiphendry
comments: true
date: 2008-04-20 17:34:21+00:00
layout: post
slug: fixing-windows-media-using-powershell
title: Fixing Windows Media Using PowerShell
wordpress_id: 67
categories:
- Microsoft
- PowerShell
- Scripting
---

I made a blunder a while back... I sync'd my laptop with my backup external hard drive but accidentally sync'd with a folder of music that contained compressed versions of the same music!! 

I may well have been able to do this in an easier fashion but I'm in the market for learning PowerShell and therefore thought I'd give it a go. Initially I focused on wanting to compare files with the same name (but different file extensions) and only delete those where the bitrates were smaller. However, reading the file tag information didn't seem to be the easiest of things and probably required writing a C# class ([www.codeproject.com](http://www.codeproject.com/) offers a variety) but they all required extra dependencies including the Windows Media Format SDK from Microsoft which I didn't really want to have to use at this point.

So I moved onto my next plan. Luckily for me the smaller files had been compressed as .wma files whilst my originals were .mp3. I could probably have just deleted everything that was .wma but I wanted to be sure I hadn't ripped anything as wma instead of mp3. So, the following script compares files with the same filenames but different extensions and deletes the .wma file if it exists and it's smaller than the mp3 I'm comparing it with. Nothing spectacular but it got the job done and I've made another step toward learning PowerShell.
    
    <span style="color:#5f9ea0;">Get-ChildItem -Recurse -Include </span><span style="color:red;">*</span><span style="color:black;">.mp3 | </span><span style="color:#5f9ea0;">ForEach-Object -Process </span><span style="color:black;">{ 
        </span><span style="color:purple;">$file </span><span style="color:red;">= </span><span style="color:navy;">$_</span><span style="color:black;">.Directory.ToString() </span><span style="color:red;">+ </span><span style="color:maroon;">"\" </span><span style="color:red;">+ </span><span style="color:navy;">$_</span><span style="color:black;">.Name.Substring(0, </span><span style="color:navy;">$_</span><span style="color:black;">.Name.LastIndexOf(</span><span style="color:maroon;">"."</span><span style="color:black;">)) </span><span style="color:red;">+ </span><span style="color:maroon;">".wma"</span><span style="color:black;">; 
        </span><span style="color:purple;">$fileinfo </span><span style="color:red;">= </span><span style="color:#5f9ea0;">New-Object -TypeName </span><span style="color:maroon;">System.IO.FileInfo </span><span style="color:#5f9ea0;">-ArgumentList </span><span style="color:purple;">$file</span><span style="color:black;">; 
        </span><span style="color:blue;">if </span><span style="color:black;">(</span><span style="color:purple;">$fileinfo</span><span style="color:black;">.Exists </span><span style="color:red;">-and </span><span style="color:purple;">$fileinfo</span><span style="color:black;">.Length </span><span style="color:red;">-le </span><span style="color:navy;">$_</span><span style="color:black;">.Length) { 
            </span><span style="color:purple;">$fileinfo</span><span style="color:black;">.MoveTo(</span><span style="color:maroon;">'C:\\Temp\\SongsToDelete\\' </span><span style="color:red;">+ </span><span style="color:purple;">$fileinfo</span><span style="color:black;">.Name) 
        } 
    }</span>

[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)
