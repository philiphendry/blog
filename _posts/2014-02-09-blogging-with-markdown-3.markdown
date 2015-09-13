---
author: admin
comments: false
date: 2014-02-09 15:28:00+00:00
excerpt: I've been meaning to blog more recently but going back to my old favourite
  blogging editor LiveWriter seemed like a step in the wrong direction seeing as I've
  been keen on Markdown and the best editor I've used Sublime Text.
layout: post
slug: blogging-with-markdown-3
title: Blogging with Markdown
wordpress_id: 402
tags:
- code
- interesting
---

I've been meaning to blog more recently but going back to my old favourite blogging editor [LiveWriter](http://windows.microsoft.com/en-gb/windows-live/essentials-other#essentials=writerother) seemed like a step in the wrong direction seeing as I've been keen on [Markdown](http://daringfireball.net/projects/markdown/syntax#link) and the best editor I've used [Sublime Text](http://www.sublimetext.com/).




The Sublime Text offerings for Markdown editing are great but combine that with blog publishing and it's not quite as good as I would like - image handling is one of my main issues.




A Google Extension, [Platen](https://chrome.google.com/webstore/detail/platen/kjldgcmgnacejmfmfdmompenlohlplod), has come to my attention and I really like it. Images are handled as easily as Ctrl+V :




[![Snippet of Platen](/assets/2014/02/snippet_of_platen2.png)](/assets/2014/02/snippet_of_platen2.png)




Code is a little harder. It supports [GitHub flavoured](https://help.github.com/articles/github-flavored-markdown) but Platen doesn't added a syntax highlighting. Therefore I'm currently using a workaround by using [Wordpress syntax](http://en.support.wordpress.com/code/posting-source-code/) where code blocks are wrapped with [code] elements. For example  :



    
    
    [code language=csharp]
    function Test(string aParameter)
    {
      Console.WriteLine("This is just a test.");
    }
    [/code]
    





will render syntax highlighted (but give I no longer host this post on WordPress I can't demonstrate!!)




Note though there are no double quotes around the language name.



