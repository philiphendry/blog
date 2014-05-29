---
author: philiphendry
comments: true
date: 2008-02-28 21:30:01+00:00
layout: post
slug: cut-paste-code-from-visual-studio-in-colour
title: Cut & Paste Code from Visual Studio in Colour
wordpress_id: 10
categories:
- Dev Tools
---

Right, kinda solved the colour cut&paste issue from my previous post... but I don't like it! I cut&pasted from Visual Studio into Word, then from word into Live Writer - however, I have to do a Paste Special otherwise the formatting is lost. Don't like this so far though since there's too much cut&pasting and now the code is double-spaced :(

// StateMachine.cpp : Defines the entry point for the console application.

//

#include "stdafx.h"

enum states {

ST_SYSTEM_OFF,

ST_LOGIN,

ST_PRODUCT_ENTRY,

ST_WRONG_PRODUCT,

ST_DISPLAY_TOPUP

};

However, there's a link below that provides a macro in VS that launches Word, pastes the code, formats it then copies to the clipboard - you've still got to Paste Special though...  

[http://addressof.com/blog/articles/samples.aspx](http://addressof.com/blog/articles/samples.aspx)  

And here's an example of the format...  

public partial class Chapter12CreatingMobileWebApps_Default : System.Web.UI.MobileControls.MobilePage

{

protected void Page_Load(object sender, EventArgs e)

{

}

}


<!-- more -->
 

Quick update. In sorting out my favourites I came across a link buried amongst them for a utility to [copy source as HTML](http://www.jtleigh.com/people/colin/software/CopySourceAsHtml/). An example of the output is shown below. I quite like this tool since you can also supply your own CSS to customise it to your liking (for example, adding a border and background colour.

public partial class MobileWebApps_Default : System.Web.UI.MobileControls.MobilePage

{

// A comment

protected void Page_Load(object sender, EventArgs e)

{

}

}
