---
author: philiphendry
comments: true
date: 2009-08-06 05:01:46+00:00
layout: post
slug: colouring-the-command-prompt
title: Colouring the Command Prompt
wordpress_id: 247
categories:
- .Net
- Code
- Scripting
---

A long time ago I wrote some really basic code to colour the output displayed in the Windows cmd.exe. I’ve just come across it hidden in one of my folders and thought I’d resurrect it since it’s something I’d like to look at in PowerShell when I have the opportunity (although changing the colour in PowerShell is easy with Out-Host.) The tool was originally knocked together because I’d wrapped grep.exe in a batch file but wasn’t happy with the output – I wanted matching strings that I was searching for to be highlighted in different colours.

 

As an example here’s some test text…

 
    
    This is a <style textcolour='red'>quick</style> test to <style textcolour='Blue'>see if</style>
    the colourise utility works and identifies
    <style textcolour='Yellow'>all</style> the necessary items.
    This is a test to check <style textcolour='Magenta'>it matches colours at the end.</style>
    <style textcolour='Yellow'>This has <style textcolour='Red'>nested <style textcolour='Blue'>colouring</style> that </style> currently </style> isn't supported.


[](http://11011.net/software/vspaste)



















































































which renders as :





[![image](http://philiphendry.files.wordpress.com/2009/08/image_thumb.png)](http://philiphendry.files.wordpress.com/2009/08/image.png)





Note that nested styles are not supported.





The code to accomplish this is below.








    
    <span style="color:blue;">using </span>System;
    <span style="color:blue;">using </span>System.Reflection;
    <span style="color:blue;">using </span>System.Collections.Generic;
    <span style="color:blue;">using </span>System.Text;
    <span style="color:blue;">using </span>System.IO;
    <span style="color:blue;">using </span>System.Text.RegularExpressions;
    
    <span style="color:blue;">namespace </span>Colourise
    {
        <span style="color:blue;">class </span><span style="color:#2b91af;">Program
        </span>{
            <span style="color:blue;">static void </span>Main(<span style="color:blue;">string</span>[] args)
            {
                <span style="color:blue;">string </span>lineInput;
                <span style="color:blue;">while </span>((lineInput = <span style="color:#2b91af;">Console</span>.ReadLine()) != <span style="color:blue;">null</span>)
                {
                    <span style="color:#2b91af;">Stack</span><<span style="color:#2b91af;">ConsoleColor</span>> oldColourStack = <span style="color:blue;">new </span><span style="color:#2b91af;">Stack</span><<span style="color:#2b91af;">ConsoleColor</span>>();
                    <span style="color:#2b91af;">Regex </span>rx = <span style="color:blue;">new </span><span style="color:#2b91af;">Regex</span>(<span style="color:#a31515;">@"(?<pretext>.*?)<style textcolour='(?<colour>[^']*)'>(?<colouredtext>.*?)</style>(?<posttext>.*?)"</span>);
                    <span style="color:#2b91af;">MatchCollection </span>matches = rx.Matches(lineInput);
                    <span style="color:blue;">if </span>(matches.Count > 0)
                    {
                        <span style="color:blue;">foreach </span>(<span style="color:#2b91af;">Match </span>match <span style="color:blue;">in </span>matches)
                        {
                            <span style="color:#2b91af;">Console</span>.Write(match.Groups[<span style="color:#a31515;">"pretext"</span>].Value);
                            oldColourStack.Push(<span style="color:#2b91af;">Console</span>.ForegroundColor);
                            <span style="color:#2b91af;">Console</span>.ForegroundColor = (<span style="color:#2b91af;">ConsoleColor</span>)<span style="color:#2b91af;">Enum</span>.Parse(<span style="color:blue;">typeof</span>(<span style="color:#2b91af;">ConsoleColor</span>), match.Groups[<span style="color:#a31515;">"colour"</span>].Value, <span style="color:blue;">true</span>);
                            <span style="color:#2b91af;">Console</span>.Write(match.Groups[<span style="color:#a31515;">"colouredtext"</span>].Value);
                            <span style="color:#2b91af;">Console</span>.ForegroundColor = oldColourStack.Pop();
                            <span style="color:#2b91af;">Console</span>.Write(match.Groups[<span style="color:#a31515;">"posttext"</span>].Value);
                        }
                        <span style="color:#2b91af;">Match </span>lastmatch = matches[matches.Count - 1];
                        <span style="color:#2b91af;">Console</span>.WriteLine(lineInput.Substring(lastmatch.Index + lastmatch.Length));
                    }
                    <span style="color:blue;">else
                    </span>{
                        <span style="color:#2b91af;">Console</span>.WriteLine(lineInput);
                    }
                }
            }
        }
    }


[](http://11011.net/software/vspaste)
