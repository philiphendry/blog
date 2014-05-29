---
author: philiphendry
comments: true
date: 2013-04-22 08:39:15+00:00
layout: post
slug: a-facebook-angularjs-directive
title: A Facebook AngularJS Directive
wordpress_id: 373
categories:
- AngularJS
---

I’ve been developing a dashboard for the application I’m working on and one of the things it needed to include were Social Media controls such as Twitter, LinkedIN, Google +1 and Facebook. Integrating them into ASP.NET WebForms and MVC was mostly a painless experience but that was because of the predominant PostBack mechanism. I’ve recently chosen to look at [AngularJS](http://angularjs.org/) since it’s two-way binding is spectacular and looked instantly better than [KnockoutJS](http://knockoutjs.com/) which seemed to require to much boilerplate code.

Most of the problems I experienced when initially trying to integrate the Facebook code revolved around AngularJS binding variables not resolving and javascript code not executing at the right time. The steps below highlight how I resolved each of these issues.

First up was initialising the Facebook SDK. I’ve placed the code below at the top of my aspx page that delivers my dashboard. The Facebook application ID is currently being fetched by .Net code at line 6. The [Facebook documentation](https://developers.facebook.com/docs/reference/javascript/) describes how to set this up as an async process and therefore does not disrupt the loading of the rest of the page.













    
    <span style="color:#606060;" id="lnum1">   1:</span> <%-- Facebook Javascript SDK --%>




    
    <span style="color:#606060;" id="lnum2">   2:</span> <div id=<span style="color:#006080;">"fb-root"</span>></div>




    
    <span style="color:#606060;" id="lnum3">   3:</span> <script>




    
    <span style="color:#606060;" id="lnum4">   4:</span>     window.fbAsyncInit = <span style="color:#0000ff;">function</span>() {




    
    <span style="color:#606060;" id="lnum5">   5:</span>         FB.init({




    
    <span style="color:#606060;" id="lnum6">   6:</span>             appId: <span style="color:#006080;">'<%=ConfigurationHelper.GetMandatorySetting<String>("facebookApplicationId")%>'</span>,




    
    <span style="color:#606060;" id="lnum7">   7:</span>             status: <span style="color:#0000ff;">true</span>, <span style="color:#008000;">// check login status</span>




    
    <span style="color:#606060;" id="lnum8">   8:</span>             cookie: <span style="color:#0000ff;">true</span>, <span style="color:#008000;">// enable cookies to allow the server to access the session</span>




    
    <span style="color:#606060;" id="lnum9">   9:</span>             xfbml: <span style="color:#0000ff;">true</span>,  <span style="color:#008000;">// parse XFBML</span>




    
    <span style="color:#606060;" id="lnum10">  10:</span>             oauth: <span style="color:#0000ff;">true</span>




    
    <span style="color:#606060;" id="lnum11">  11:</span>         });




    
    <span style="color:#606060;" id="lnum12">  12:</span>     };




    
    <span style="color:#606060;" id="lnum13">  13:</span>     (<span style="color:#0000ff;">function</span> (d, s, id) {




    
    <span style="color:#606060;" id="lnum14">  14:</span>         <span style="color:#0000ff;">var</span> js, fjs = d.getElementsByTagName(s)[0];




    
    <span style="color:#606060;" id="lnum15">  15:</span>         <span style="color:#0000ff;">if</span> (d.getElementById(id)) <span style="color:#0000ff;">return</span>;




    
    <span style="color:#606060;" id="lnum16">  16:</span>         js = d.createElement(s); js.id = id;




    
    <span style="color:#606060;" id="lnum17">  17:</span>         js.src = <span style="color:#006080;">"//connect.facebook.net/en_GB/all.js"</span>;




    
    <span style="color:#606060;" id="lnum18">  18:</span>         fjs.parentNode.insertBefore(js, fjs);




    
    <span style="color:#606060;" id="lnum19">  19:</span>     }(document, <span style="color:#006080;">'script'</span>, <span style="color:#006080;">'facebook-jssdk'</span>));




    
    <span style="color:#606060;" id="lnum20">  20:</span> </script>
















The next step is to define the AngularJS directive. There are three things to note :



	
  1. The template includes the binding {{urlToLike}} on line 5 which is resolved during the linking phase and must appear on the scope of the containing controller.

	
  2. The template would never turn into a Facebook plugin unless we tell it to change and therefore on line 13 I call the Facebook SDK to [parse](https://developers.facebook.com/docs/reference/javascript/FB.XFBML.parse/) the element just added. Thanks to [echong](https://gist.github.com/echong/3856847) for that hint. The check for the existence of FB is required because sometimes the SDK hasn’t been configured yet and if that’s the case the SDK will render the like button itself.

	
  3. The final trick on line 11 was to make the call to parse inside a $timeout() – this adds it to the javascript queue and is processed when everything else has finished. This particular change solved problems for me when I refreshed the entire page by pressing F5 – the refresh caused the SDK to be loaded after the directive and therefore the $timeout() sorts the ordering out nicely. I’ve used this trick before to render Highcharts after the rest of the AngularJS digest loop has completed.



















    
    <span style="color:#606060;" id="lnum1">   1:</span> .directive(<span style="color:#006080;">'facebookLike'</span>, [<span style="color:#006080;">'$timeout'</span>, <span style="color:#0000ff;">function</span> ($timeout) {




    
    <span style="color:#606060;" id="lnum2">   2:</span>     <span style="color:#0000ff;">return</span> {




    
    <span style="color:#606060;" id="lnum3">   3:</span>         template:




    
    <span style="color:#606060;" id="lnum4">   4:</span>             <span style="color:#006080;">'<div class="fb-like" '</span> +




    
    <span style="color:#606060;" id="lnum5">   5:</span>                 <span style="color:#006080;">'data-href="{{marketingSiteUrl}}" '</span> +




    
    <span style="color:#606060;" id="lnum6">   6:</span>                 <span style="color:#006080;">'data-send="false" '</span> +




    
    <span style="color:#606060;" id="lnum7">   7:</span>                 <span style="color:#006080;">'data-layout="button_count" '</span> +




    
    <span style="color:#606060;" id="lnum8">   8:</span>                 <span style="color:#006080;">'data-width="450" '</span> +




    
    <span style="color:#606060;" id="lnum9">   9:</span>                 <span style="color:#006080;">'data-show-faces="false"></div>'</span>,




    
    <span style="color:#606060;" id="lnum10">  10:</span>         link: <span style="color:#0000ff;">function</span> (scope, element, attributes) {




    
    <span style="color:#606060;" id="lnum11">  11:</span>             $timeout(<span style="color:#0000ff;">function</span> () {




    
    <span style="color:#606060;" id="lnum12">  12:</span>                 <span style="color:#0000ff;">return</span> <span style="color:#0000ff;">typeof</span> FB !== <span style="color:#006080;">"undefined"</span> && FB !== <span style="color:#0000ff;">null</span>




    
    <span style="color:#606060;" id="lnum13">  13:</span>                     ? FB.XFBML.parse(element.parent()[0])




    
    <span style="color:#606060;" id="lnum14">  14:</span>                     : <span style="color:#0000ff;">void</span> 0; });




    
    <span style="color:#606060;" id="lnum15">  15:</span>         }




    
    <span style="color:#606060;" id="lnum16">  16:</span>     };




    
    <span style="color:#606060;" id="lnum17">  17:</span> }])
















This same technique above can be used for LinkedIN, Twitter and Google +1 but the parse functions are slighty different (IN.init(), twttr.widgets.load() and gapi.plusone.go() respectively.)
