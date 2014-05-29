---
author: philiphendry
comments: true
date: 2011-02-10 10:48:04+00:00
layout: post
slug: displaying-unsaved-changes-message-for-a-web-page-using-jquery
title: Displaying ‘Unsaved Changes’ message for a web page using jQuery
wordpress_id: 345
categories:
- ASP.NET
- jQuery
- Web
---

I had a need to allow a user to cancel navigation to another page if changes to a form had not yet been saved – especially if they attempted to navigate to another page from the ever present menu. The code I used has a slight ‘hack’ which involves using the propertychange DOM event in IE rather than the change event which I would have usually expected. 

 

Here’s the code that sets a global flag to indicate there’s an unsaved change :

 
    
    <span style="color:#0000FF;">var</span><span style="color:#000000;"> _changesMade </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">false</span><span style="color:#000000;">;
    $(document).ready(</span><span style="color:#0000FF;">function</span><span style="color:#000000;">() {
    
       $(</span><span style="color:#000000;">'</span><span style="color:#000000;">form</span><span style="color:#000000;">'</span><span style="color:#000000;">).bind($.browser.msie </span><span style="color:#000000;">?</span><span style="color:#000000;"> </span><span style="color:#000000;">'</span><span style="color:#000000;">propertychange</span><span style="color:#000000;">'</span><span style="color:#000000;"> : </span><span style="color:#000000;">'</span><span style="color:#000000;">change</span><span style="color:#000000;">'</span><span style="color:#000000;">, </span><span style="color:#0000FF;">function</span><span style="color:#000000;">() {
           _changesMade </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">true</span><span style="color:#000000;">;
       });
    
       $(window).bind(</span><span style="color:#000000;">'</span><span style="color:#000000;">beforeunload</span><span style="color:#000000;">'</span><span style="color:#000000;">, </span><span style="color:#0000FF;">function</span><span style="color:#000000;">() {
           </span><span style="color:#0000FF;">if</span><span style="color:#000000;"> (_changesMade)
               </span><span style="color:#0000FF;">return</span><span style="color:#000000;"> </span><span style="color:#000000;">'</span><span style="color:#000000;">There are unsaved changes which will be lost if you continue.</span><span style="color:#000000;">'</span><span style="color:#000000;">;
       });
    });</span>





There are a couple of caveats. Any button that actually does the save will need a OnClientClick that sets the _changesMade to false to prevent the message from being displayed :




    
    <span style="color:#0000FF;"><</span><span style="color:#800000;">asp:Button </span><span style="color:#FF0000;">ID</span><span style="color:#0000FF;">="btnSubmit"</span><span style="color:#FF0000;"> runat</span><span style="color:#0000FF;">="server"</span><span style="color:#FF0000;"> Text</span><span style="color:#0000FF;">="Submit"</span><span style="color:#FF0000;"> CssClass</span><span style="color:#0000FF;">="button"</span><span style="color:#FF0000;"> OnClick</span><span style="color:#0000FF;">="btnSubmit_Click"</span><span style="color:#FF0000;"> 
        OnClientClick</span><span style="color:#0000FF;">="_changesMade=false; return true;"</span><span style="color:#FF0000;"> </span><span style="color:#0000FF;">/></span><span style="color:#000000;"> 
    
    </span>





Also, if you have any code that causes the change event to fire (DOM manipulation after an Ajax call) then you might have to save the state of the _changesMade flag, make the DOM changes then reset the flag to it’s original value.
