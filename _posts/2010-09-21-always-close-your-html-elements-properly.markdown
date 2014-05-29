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

```
<head runat="server">
    <script type="text/javascript" language="javascript" src="GetResource.ashx?../lib/jquery/1.3.2/jquery-1.3.2.js" />
    <script type="text/javascript" language="javascript" src="GetResource.ashx?../lib/jqueryplugins/jquery.blockUI.js" />
    <script type="text/javascript" language="javascript" src="GetResource.ashx?../rms.utility.web.jqueryplugins/jquery.rms.ajaxprogress.js" />
</head>
```

and this :

```
<head runat="server">
    <script type="text/javascript" language="javascript" src="GetResource.ashx?../lib/jquery/1.3.2/jquery-1.3.2.js"></script>
    <script type="text/javascript" language="javascript" src="GetResource.ashx?../lib/jqueryplugins/jquery.blockUI.js"></script>
    <script type="text/javascript" language="javascript" src="GetResource.ashx?../rms.utility.web.jqueryplugins/jquery.rms.ajaxprogress.js"></script>
</head>
```


In fact, it looks like my syntax highlighter I’m using in LiveWriter has spotted the difference too! Basically because I didn’t close the script element with a </script> IE only executed the first javascript fetch, Firefox wouldn’t execute any and Chrome worked!! Go figure!
