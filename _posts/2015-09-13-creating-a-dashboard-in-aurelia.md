---
author: Philip Hendry
title: Creating a Dashboard in Aurelia
date: 2015-09-13 18:02
comments: true
layout: post
tags:
- aurelia
---
I've been using [Aurelia](http://aurelia.io/) for a few weeks now and I'm certainly appreciating it this far and feel like I'm having a lot less trouble understanding it than when I was learning [AngularJS 1.x](https://angularjs.org/).

This post describes the steps I've taken to build a dashboard which I currently already have working in production using AngularJS but wanted to re-write in Aurelia as an experiment to see how well it works.

The dashboard looks a little like this:

	+-------------------------------+
	| +---------------------------+ |
	| |         Carousel          | |
	| |                           | |
	| +---------------------------+ |
	| +------------+ +------------+ |
	| | Shortcuts  | | Stats      | |
	| |            | |            | |
	| |            | |            | |
	| |            | |            | |
	| |            | |            | |
	| |            | |            | |
	| +------------+ +------------+ |
	|                               |
	| +------------+ +------------+ |
	| | Results    | | Tweets     | |
	| |            | |            | |
	| |            | |            | |
	| |            | |            | |
	| |            | |            | |
	| +------------+ +------------+ |
	+-------------------------------+
	
It's a take on master-detail where the carousel is the master and the other four _widgets_ are the detail. Changing the carousel to a new item updates all the _widgets_ below possibly hiding some that aren't applicable.

## Binding Master to Detail
So in this example I have a `dashboard.html` and at the top of this template there is the `<carousel>`:

    <require from="./carousel"></require>
    <carousel
      items.bind="items"
      template="./carousel-item.html"/>
    </carousel>

I'll create [another post](http://blog.philiphendry.me.uk/2015/09/13/creating-a-carousel-in-aurelia/) regarding the `<carousel>` but for now the important point here is that the `items` attribute is bound to `items` on the view model `dashboard.js`:

    export class DashboardViewModel {
       items = [];
       selectedItem = null;
    }

So this view model defines a `selectedItem` which will be kept up-to-date by the carousel (there's code I'm leaving out here but that's for the carousel blog :) )

### The Importance of Show and If
Now back to those widgets. I'll expand the `dashboard.html` template further:

    <div class="row" show.bind="selectedItem">
      <div class="col-md-6">        
        <!-- Widgets goes here -->
      </div>
      <div class="col-md-6">
        <!-- Widgets goes here -->
      </div>
    </div>

This defines the [Bootstrap](http://getbootstrap.com/) grid. But there's something important to note. I've bound the `div` attribute `show` to the `selectedItem` from the view model so that in the instance when the form is first drawn the widgets aren't drawn.

However, binding to `show` means that the framework continues to create the widgets beneath it and for me this was important so the widgets could initialise. If I'd bound to `if` the initialise happens later and I miss out on important events.

### Create a Widget
The dashboard contains functionality for _Shortcuts_, _Stats_, _Results_,  and _Tweets_. I want each of those components to be agnostic of the dashboard and I want them to be displayed in a nice styled widget control with a header and icon.

So in `dashboard.html` I want to be able to say something along the lines of this:

    <div class="row" ...>
      <widget 
	      title="Shortcuts" 
	      icon="fa-bookmark"
	      show.bind="???">
	    <shortcuts 
	        item.bind="selectedItem"
	    </shortcuts>
	  </widget>
	  ...
	</div>

So `<widget>` will bring along the styling and `<shortcuts>` brings functionality but there are a couple of snags. 

1. The widget handles visibility but doesn't know when it should be so it will need to delegate that with a binding.
2. The shortcuts will bind to the `selectedItem` of the dashboard but it can't access that now since it's inside another component.

It's easiest to show the solution and explain how it hangs together so here's the dashboard code but corrected:

    <div class="row" ...>
      <widget
          title="Shortcuts"
          icon="fa-bookmark"
          show.bind="showShortcuts">
        <template replace-part="item-template">
          <shortcuts
            item.bind="$parent.selectedEvent"
            visible.two-way="$parent.showShortcuts">
          </shortcuts>
        </template>
      </widget>
      ...
    </div>

The widget is fairly basic so I'll just show that without comment. Here's `widget.html`:

	<template>
		<div class="widget">
			<div class="widget-header">
				<i class="fa ${icon}"></i>
				<h3>${title}</h3>
			</div>
			<div class="widget-content">
				<template 
				  replaceable 
				  part="item-template">
				</template>
			</div>
		</div>
	</template>

The view model `widget.js`:

	import "./widget.css!";
	import {bindable} from "aurelia-framework";
	
	export class Widget2CustomElement {
		@bindable title;
		@bindable icon;

		bind(bindingContext) {
			this.$parent = bindingContext;
		}
	}

I've omitted `widget.css` since it's rather large and not really relevant to this dicussion.

### Skipping Generations
What is important in that last code snippet is the `bind()` event which stores the `bindingContext`. It wasn't immediately obvious to me in the documentation but `$parent` isn't a built-in property but must be declared.

Now that the widget has a `$parent` property we can solve the problem of components needing to access another component it's contained within but several levels down. In this instance my `shortcuts` can now bind  it's `item` property to `$parent.selectedItem` via the widget. Admittedly we might end up with a chain of `$parent.$parent.$parent` but I think that's acceptable.

### Auto-Properties on Parents
The next conundrum was how `<shortcuts>` can tell the widget whether it should be visible or not. The solution is already shown above but amounts to the following:

    <shortcuts 
	  visible.two-way="$parent.showShortcuts"
	  ...
	/>

`shortcuts.js` defines a `@bindable visible` property which is then bound to a property on `$parent`. I've declared `showShortcuts` in `dashboard.js` and then the widget binds to this property:

    <widget
      show.bind="showShortcuts
      ...
    />

So I'm piggy-backing off `<dashboard>` to 'transmit' the information from one component to another which isn't perfect since I'd rather it be direct but it is simple and in this particular instance obvious.

### Closing
I've covered a couple of methods I've used to pass data between components and I'll to share more in future posts. My dashboard also renders graphs using [Highcharts](http://www.highcharts.com/) so I'll cover that too.

Hopefully you've read something useful but please do comment on alternatives if you know of any :)