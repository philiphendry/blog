---
author: philiphendry
comments: true
date: 2010-09-14 20:31:28+00:00
layout: post
slug: implementing-a-jquery-browser-history-plugin-using-microsoft-ajax-history
title: Implementing a jQuery Browser history Plugin using microsoft Ajax History
wordpress_id: 322
tags:
- asp-net
- jquery
---

This article is a summary of the steps I took to produce a jQuery plugin that would create history points in the browsers history programmatically. In particular I wanted to create as simple an interface in the implementing page as possible and primarily needed to integrate with ASP.NET.

 

I will also describe my workaround for a bug in Microsoft Ajax ASP.NET 3.5 where the onNavigate event would not fire when the page was too large since the _onIFrameLoad code injected by the ScriptManager would execute before the ajax initialisation had time to run.

 

The first thing I did was consider the interface I wanted to write in each of the aspx pages that required history. In particular I wanted to :

 

  
  1. Only have to enter the code to achieve history in one location
   
  2. Encapsulate as much of the complexities of maintaining browser history as possible.
   
  3. Allow the plugin to be extendable without over-engineering it!
 

I decided that keeping it client-side and written in javascript would be the best approach and using my favoured library, jQuery, would simplify integration. And this is the interface I came up with :

 
    
```
<script type="text/javascript" language="javascript">
   $(document).ready(function() {
       $.fn.ajaxHistory.registerCallbacks(getHistory, setHistory, refreshHistory);
       $("#<%=ddlStore.ClientID%>").ajaxHistory();
       $("#<%=btnSearch.ClientID%>").ajaxHistory();
   });
   
   function getHistory() {
       return {
           "store": $("#<%=ddlStore.ClientID%>").val(),
           "surname": $("#<%=txbSurname.ClientID%>").val()
       };
   }

   function setHistory(state) {
       $("#<%=ddlStore.ClientID%>").val(state.store || "0");
       $("#<%=txbSurname.ClientID%>").val(state.surname || "");
   }

   function refreshHistory() {
       $("#<%=btnSearch.ClientID%>").click();
   }
</script>
```



This code his very basic indeed and doesn’t really include an functionality whatsoever. The ready() function registers three callback with the refreshHistory callback optional. getHistory() is called to return a javascript object containing all the data that must be stored in order to return the page back to a the current state. setHistory() performs the reverse of getHistory(). And refreshHistory() is called upon to refresh the page using the restored state and therefore fetch any updates from a server. In this particular example, the page is a search screen where the user can select a store from a drop down list (ddlStore) which is configured for AutoPostBack and refreshes the search results or they can enter a surname and click the search button (btnSearch.) In the ready() I therefore tell .ajaxHistory(), my plugin, the controls that, when clicked or changed, are required to mark a history point.





The plugin itself is written using a standard pattern and over all looks like this :




```
(function($) {
    var _elements = new Array();

    $.fn.ajaxHistory = function() {
        return this.each(function() {
            var element = "#" + this.id;
            bindEvent(element);
            _elements.push(element);
        });
    };

    $.fn.ajaxHistory.initialise = function() {
        Sys.Application.add_navigate(onNavigate);
        Sys.WebForms.PageRequestManager.getInstance().add_endRequest(onEndRequest);
    };


    // onEndRequest will be called each time an ajax call is completed and therefore we can rebind all the events.
    function onEndRequest(sender, e) {
        $.each(_elements, function(index, item) {
            bindEvent(item);
        });
    };

})(jQuery);

$(document).ready(function() {
    $.fn.ajaxHistory.initialise();
});
```




The plugin initialises a couple of Microsoft Ajax events in its own ready() method and the ajaxHistory() method binds the passed elements (specified by a selector) to events that inform the plug in when the element is clicked (buttons, links) or changed (textboxes, drop downs). Since my plugin is used within UpdatePanels the onEndRequest() will be fired at the end of each Ajax call and re-bind the controls (I would use the jQuery .live() binding however binding the change event in IE using jQuery 1.4.2 seems to be broken at the moment and therefore I have to revert to jQuery 1.3.2 instead.)





To remember history points I’m making use of the history functionality built into Microsoft Ajax. This requires that the ScriptManager control is set with the attribute EnableHistory turned on :




```
<asp:ScriptManager ID="ScriptManager1" runat="server" EnableHistory="true" />
```








The Microsoft Ajax history functionality is then used in the plugin when the events bound to each of the elements is fired and simply adds a history point calling the getHistory() callback. This code store the state as a query string preceeded with a # which forces the browser to log it in it’s history and can even be bookmarked :






  
    
```
Sys.Application.addHistoryPoint(_getHistory(), document.title);
```







When the user clicks Back in the browser window, Microsoft Ajax will automatically detect the state after the # and raise the onNavigate event passing the state to it. The plugin simply passes that state to the setHistory() callback and then calls the refresh callback.





### Microsoft Ajax Bug





[Josh Close](http://weblogs.asp.net/joshclose/archive/2008/11/11/asp-net-ajax-addhistorypoint-bug.aspx) has already blogged about the bug I had whilst writing this plugin. My initial testing didn’t have any problems but when I tried to integrate my plugin to a larger page I suddenly found the onNavigate event would not fire and therefore my page would not return to a previous state.





My own debugging in the Microsoft Ajax javascript lead me to the _onIFrameLoad javascript that loads the state and it was Josh’s article that helped me understand that it was running too early. To solve this problem I made sure that the history was initialised as soon as possible rather than last in the page and therefore I made the call to _enableHistoryInScriptManager() myself immediately after the ScriptManager. Thankfully this method doesn’t do much anyway but ensures the write state is set up for the onNavigate event to be fired as expected.




    
```    
<asp:ScriptManager ID="ScriptManager1" runat="server" EnableHistory="true" />
<script type="text/javascript" language="javascript">
    Sys.Application._enableHistoryInScriptManager();
</script>
```





Hopefully this problem will go away in ASP.NET 4!
