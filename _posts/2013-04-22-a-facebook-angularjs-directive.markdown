---
author: philiphendry
comments: true
date: 2013-04-22 08:39:15+00:00
layout: post
slug: a-facebook-angularjs-directive
title: A Facebook AngularJS Directive
wordpress_id: 373
tags:
- angularjs
---

I’ve been developing a dashboard for the application I’m working on and one of the things it needed to include were Social Media controls such as Twitter, LinkedIN, Google +1 and Facebook. Integrating them into ASP.NET WebForms and MVC was mostly a painless experience but that was because of the predominant PostBack mechanism. I’ve recently chosen to look at [AngularJS](http://angularjs.org/) since it’s two-way binding is spectacular and looked instantly better than [KnockoutJS](http://knockoutjs.com/) which seemed to require to much boilerplate code.

Most of the problems I experienced when initially trying to integrate the Facebook code revolved around AngularJS binding variables not resolving and javascript code not executing at the right time. The steps below highlight how I resolved each of these issues.

First up was initialising the Facebook SDK. I’ve placed the code below at the top of my aspx page that delivers my dashboard. The Facebook application ID is currently being fetched by .Net code at line 6. The [Facebook documentation](https://developers.facebook.com/docs/reference/javascript/) describes how to set this up as an async process and therefore does not disrupt the loading of the rest of the page.

```
<%-- Facebook Javascript SDK --%>
<div id="fb-root"></div>
<script>
	window.fbAsyncInit = function() {
		FB.init({
			appId: '<%=ConfigurationHelper.GetMandatorySetting<String>("facebookApplicationId")%>',
			status: true, // check login status
			cookie: true, // enable cookies to allow the server to access the session
			xfbml: true, // parse XFBML
			oauth: true
		});
	};
	(function (d, s, id) {
		var js, fjs = d.getElementsByTagName(s)[0];
		if (d.getElementById(id)) return;
			js = d.createElement(s); js.id = id;
			js.src = "//connect.facebook.net/en_GB/all.js";
			fjs.parentNode.insertBefore(js, fjs);
		}(document, 'script', 'facebook-jssdk'));
</script>
```


The next step is to define the AngularJS directive. There are three things to note :



	
  1. The template includes the binding {{urlToLike}} on line 5 which is resolved during the linking phase and must appear on the scope of the containing controller.

	
  2. The template would never turn into a Facebook plugin unless we tell it to change and therefore on line 13 I call the Facebook SDK to [parse](https://developers.facebook.com/docs/reference/javascript/FB.XFBML.parse/) the element just added. Thanks to [echong](https://gist.github.com/echong/3856847) for that hint. The check for the existence of FB is required because sometimes the SDK hasn’t been configured yet and if that’s the case the SDK will render the like button itself.

	
  3. The final trick on line 11 was to make the call to parse inside a $timeout() – this adds it to the javascript queue and is processed when everything else has finished. This particular change solved problems for me when I refreshed the entire page by pressing F5 – the refresh caused the SDK to be loaded after the directive and therefore the $timeout() sorts the ordering out nicely. I’ve used this trick before to render Highcharts after the rest of the AngularJS digest loop has completed.

```
.directive('facebookLike', ['$timeout', function ($timeout) {
	return {
		template:
			'<div class="fb-like" ' +
				'data-href="{{marketingSiteUrl}}" ' +
				'data-send="false" ' +
				'data-layout="button_count" ' +
				'data-width="450" ' +
				'data-show-faces="false"></div>',
		link: function (scope, element, attributes) {
 			$timeout(function () {
 				return typeof FB !== "undefined" && FB !== null
 					? FB.XFBML.parse(element.parent()[0])
 					: void 0; });
 			}
 	};
 }])
 ```


This same technique above can be used for LinkedIN, Twitter and Google +1 but the parse functions are slighty different (IN.init(), twttr.widgets.load() and gapi.plusone.go() respectively.)
