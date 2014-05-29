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

```
var _changesMade = false; 
$(document).ready(function() {  
	$('form').bind($.browser.msie ? 'propertychange' : 'change', 
		function() { _changesMade = true; 
	});  
	
	$(window).bind('beforeunload', function() { 
		if (_changesMade) 
			return 'There are unsaved changes which will be lost if you continue.'; 
	}); 
});
```

There are a couple of caveats. Any button that actually does the save will need a OnClientClick that sets the _changesMade to false to prevent the message from being displayed :


```
<asp:Button ID="btnSubmit" runat="server" Text="Submit" CssClass="button" OnClick="btnSubmit_Click"  OnClientClick="_changesMade=false; return true;" /> 
```

Also, if you have any code that causes the change event to fire (DOM manipulation after an Ajax call) then you might have to save the state of the _changesMade flag, make the DOM changes then reset the flag to it’s original value.
