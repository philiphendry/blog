---
author: Philip Hendry
title: PowerShell Calculated Properties Used to Count Lines of Code
date: 2015-11-23 14:00
comments: true
layout: post
tags:
- powershell
---
I've completely committed myself to PowerShell in the last few months which means when I've got a problem that should be scripted I'll stick to my guns and make it work with PowerShell.

I was trying to estimate some changes to some files in an existing Visual Studio .Net solution and turned to PowerShell for some statistics. My first question was easy, how many files with the extension `aspx` do I have:

```powershell
    > ls *.aspx -recurse | measure
```

which gives a useful output of:

    Count    : 42
    Average  :
    Sum      :
    Maximum  :
    Minimum  :
    Property :

What I then wanted was a count of lines across all those files:

```powershell
    ls *.aspx -Recurse 
        | % { $_ | Get-Content | Measure -Line } 
        | % lines 
        | measure -Sum
```

I've put some linebreaks in for readability but the trick here was to use `%` and `$_` to pipe each file through `Get-Content` to make used of `Measures` line counting ability. Then I isolate the lines property with `% lines` and sum to get:

```
    Count    : 42
    Average  :
    Sum      : 3795
    Maximum  :
    Minimum  :
    Property :
```

The next and final step takes this script and turns it into one that counts and groups the number of lines of code across multiple file types. Here's the code:

```powershell
    ls *.aspx,*.ascx,*.cs -r 
      | select Extension, @{ 
        name="LineCount"; 
        expression = { $_ | get-content | measure -Line | % Lines }  
      } 
      | Group -Property Extension 
      | select Name, @{ 
        name="LineCount"; 
        expression = { $_.Group | Measure -Sum -Property LineCount | % Sum } 
      }
```

And the output:

```
    Name  LineCount
    ----  ---------
    .aspx      3795
    .ascx       262
    .cs       24034
```

The key break through for me to write this was the use of [calculated properties](https://technet.microsoft.com/en-us/library/ff730948.aspx) which are added in two of the `select` commands and have the syntax of `@{ name=""; expression={}`. The first `select` simply returns an object, one for each file found by `ls`, with the file extension and the count of lines in that file. After the `group` command the output looks like this:

```
    Count Name                      Group
    ----- ----                      -----
       42 .aspx                     {@{Extension=.aspx; LineCount=107}, @{Extension=.aspx; LineCount=112}, @{Extension=.aspx; LineCount=114}...
       10 .ascx                     {@{Extension=.ascx; LineCount=3}, @{Extension=.ascx; LineCount=6}, @{Extension=.ascx; LineCount=18}, @{E...
      165 .cs                       {@{Extension=.cs; LineCount=75}, @{Extension=.cs; LineCount=23}
```

The purpose of the second `select` is to iterate through each group summing just the `LineCount` properties with a final `% sum` to isolate just the sum.

And before I finish, some of this was written with the help of [Microsoft Reference](https://www.microsoft.com/en-gb/download/confirmation.aspx?id=42554) which may well be used a lot more in the future :)
