---
author: philiphendry
comments: true
date: 2010-09-21 09:16:34+00:00
layout: post
slug: always-close-your-html-elements-properly
title: Always close Your HTML Elements Properly
wordpress_id: 332
categories:
- ASP.NET
---

I’ve just had a strange error trying to write a noddy ASP.NET page using Microsoft Ajax. The error itself was _Microsoft JScript runtime error: Object required_ which basically tells you nothing. This error was being thrown in _Sys$WebForms$PageRequestManager$_initializeInternal_ by the line trying to access _this._form.onsubmit_. It would appear _form is null! Looking up the call stack it originated from the Ajax initialize call : _Sys.WebForms.PageRequestManager._initialize('scriptManager', document.getElementById('form1'));_. It would appear that the form element has not been closed yet and therefore the document.getElementById is searching for something that doesn’t exist.

 

Ok, that tells me nothing.

 

Thankfully I had an epiphany which worked (for once!) Spot the difference between this :

 

  
    
    <span style="color:#0000FF;"></</span><span style="color:#800000;">title</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">script </span><span style="color:#FF0000;">type</span><span style="color:#0000FF;">="text/javascript"</span><span style="color:#FF0000;"> language</span><span style="color:#0000FF;">="javascript"</span><span style="color:#FF0000;"> src</span><span style="color:#0000FF;">="GetResource.ashx?../lib/jquery/1.3.2/jquery-1.3.2.js"</span><span style="color:#FF0000;"> </span><span style="color:#0000FF;">/></span><span style="background-color:#F5F5F5;color:#000000;">
        </span><span style="background-color:#F5F5F5;color:#000000;"><</span><span style="background-color:#F5F5F5;color:#000000;">script type</span><span style="background-color:#F5F5F5;color:#000000;">=</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">text/javascript</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;"> language</span><span style="background-color:#F5F5F5;color:#000000;">=</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">javascript</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;"> src</span><span style="background-color:#F5F5F5;color:#000000;">=</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">GetResource.ashx?../lib/jqueryplugins/jquery.blockUI.js</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;"> </span><span style="background-color:#F5F5F5;color:#000000;">/</span><span style="background-color:#F5F5F5;color:#000000;">></span><span style="background-color:#F5F5F5;color:#000000;">
    </span><span style="background-color:#F5F5F5;color:#000000;">    </span><span style="background-color:#F5F5F5;color:#000000;"><</span><span style="background-color:#F5F5F5;color:#000000;">script type</span><span style="background-color:#F5F5F5;color:#000000;">=</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">text/javascript</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;"> language</span><span style="background-color:#F5F5F5;color:#000000;">=</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">javascript</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;"> src</span><span style="background-color:#F5F5F5;color:#000000;">=</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">GetResource.ashx?../rms.utility.web.jqueryplugins/jquery.rms.ajaxprogress.js</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;"> </span><span style="background-color:#F5F5F5;color:#000000;">/</span><span style="background-color:#F5F5F5;color:#000000;">></span><span style="background-color:#F5F5F5;color:#000000;">
    </span><span style="background-color:#F5F5F5;color:#000000;"><</span><span style="background-color:#F5F5F5;color:#000000;">/</span><span style="background-color:#F5F5F5;color:#000000;">head></span>








and this :




    
    <span style="color:#0000FF;"><</span><span style="color:#800000;">head </span><span style="color:#FF0000;">runat</span><span style="color:#0000FF;">="server"</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">title</span><span style="color:#0000FF;">></span><span style="color:#000000;">jquery.rms.ajaxprogress.js Tests</span><span style="color:#0000FF;"></</span><span style="color:#800000;">title</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">script </span><span style="color:#FF0000;">type</span><span style="color:#0000FF;">="text/javascript"</span><span style="color:#FF0000;"> language</span><span style="color:#0000FF;">="javascript"</span><span style="color:#FF0000;"> src</span><span style="color:#0000FF;">="GetResource.ashx?../lib/jquery/1.3.2/jquery-1.3.2.js"</span><span style="color:#0000FF;">></</span><span style="color:#800000;">script</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">script </span><span style="color:#FF0000;">type</span><span style="color:#0000FF;">="text/javascript"</span><span style="color:#FF0000;"> language</span><span style="color:#0000FF;">="javascript"</span><span style="color:#FF0000;"> src</span><span style="color:#0000FF;">="GetResource.ashx?../lib/jqueryplugins/jquery.blockUI.js"</span><span style="color:#0000FF;">></</span><span style="color:#800000;">script</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">script </span><span style="color:#FF0000;">type</span><span style="color:#0000FF;">="text/javascript"</span><span style="color:#FF0000;"> language</span><span style="color:#0000FF;">="javascript"</span><span style="color:#FF0000;"> src</span><span style="color:#0000FF;">="GetResource.ashx?../rms.utility.web.jqueryplugins/jquery.rms.ajaxprogress.js"</span><span style="color:#0000FF;">></</span><span style="color:#800000;">script</span><span style="color:#0000FF;">></span><span style="color:#000000;">
    </span><span style="color:#0000FF;"></</span><span style="color:#800000;">head</span><span style="color:#0000FF;">></span><span style="color:#000000;">
    </span>





In fact, it looks like my syntax highlighter I’m using in LiveWriter has spotted the difference too! Basically because I didn’t close the script element with a </script> IE only executed the first javascript fetch, Firefox wouldn’t execute any and Chrome worked!! Go figure!
