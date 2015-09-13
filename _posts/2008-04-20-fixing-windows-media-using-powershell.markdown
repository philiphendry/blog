---
author: philiphendry
comments: true
date: 2008-04-20 17:34:21+00:00
layout: post
slug: fixing-windows-media-using-powershell
title: Fixing Windows Media Using PowerShell
wordpress_id: 67
tags:
- powershell
- scripting
---

I made a blunder a while back... I sync'd my laptop with my backup external hard drive but accidentally sync'd with a folder of music that contained compressed versions of the same music!! 

I may well have been able to do this in an easier fashion but I'm in the market for learning PowerShell and therefore thought I'd give it a go. Initially I focused on wanting to compare files with the same name (but different file extensions) and only delete those where the bitrates were smaller. However, reading the file tag information didn't seem to be the easiest of things and probably required writing a C# class ([www.codeproject.com](http://www.codeproject.com/) offers a variety) but they all required extra dependencies including the Windows Media Format SDK from Microsoft which I didn't really want to have to use at this point.

So I moved onto my next plan. Luckily for me the smaller files had been compressed as .wma files whilst my originals were .mp3. I could probably have just deleted everything that was .wma but I wanted to be sure I hadn't ripped anything as wma instead of mp3. So, the following script compares files with the same filenames but different extensions and deletes the .wma file if it exists and it's smaller than the mp3 I'm comparing it with. Nothing spectacular but it got the job done and I've made another step toward learning PowerShell.
    
```
Get-ChildItem -Recurse -Include *.mp3 | ForEach-Object -Process { 
    $file = $_.Directory.ToString() + "\" + $_.Name.Substring(0, $_.Name.LastIndexOf(".")) + ".wma"; 
    $fileinfo = New-Object -TypeName System.IO.FileInfo -ArgumentList $file; 
    if ($fileinfo.Exists -and $fileinfo.Length -le $_.Length) { 
        $fileinfo.MoveTo('C:\\Temp\\SongsToDelete\\' + $fileinfo.Name) 
    } 
}
```