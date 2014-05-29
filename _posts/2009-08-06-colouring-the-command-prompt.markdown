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



which renders as :

[![image](http://philiphendry.files.wordpress.com/2009/08/image_thumb.png)](http://philiphendry.files.wordpress.com/2009/08/image.png)

Note that nested styles are not supported.

The code to accomplish this is below.
    
```
using System;
using System.Reflection;
using System.Collections.Generic;
using System.Text;
using System.IO;
using System.Text.RegularExpressions;

namespace Colourise
{
    class Program
    {
        static void Main(string[] args)
        {
            string lineInput;
            while ((lineInput = Console.ReadLine()) != null)
            {
                Stack<ConsoleColor> oldColourStack = new Stack<ConsoleColor>();
                Regex rx = new Regex(@"(?<pretext>.*?)<style textcolour='(?<colour>[^']*)'>(?<colouredtext>.*?)</style>(?<posttext>.*?)");
                MatchCollection matches = rx.Matches(lineInput);
                if (matches.Count > 0)
                {
                    foreach (Match match in matches)
                    {
                        Console.Write(match.Groups["pretext"].Value);
                        oldColourStack.Push(Console.ForegroundColor);
                        Console.ForegroundColor = (ConsoleColor)Enum.Parse(typeof(ConsoleColor), match.Groups["colour"].Value, true);
                        Console.Write(match.Groups["colouredtext"].Value);
                        Console.ForegroundColor = oldColourStack.Pop();
                        Console.Write(match.Groups["posttext"].Value);
                    }
                    Match lastmatch = matches[matches.Count - 1];
                    Console.WriteLine(lineInput.Substring(lastmatch.Index + lastmatch.Length));
                }
                else
                {
                    Console.WriteLine(lineInput);
                }
            }
        }
    }
}
```