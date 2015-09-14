---
author: Philip Hendry
title: Creating a Carousel in Aurelia
date: 2015-09-13 20:39
comments: true
layout: post
tags:
- aurelia
---
This post hopes to describe how to create a basic carousel component from scratch for use with Aurelia. The article begins with setting up the environment but this could easily be skipped. If you just want to see some code then it's all in [github](https://www.github.com/philiphendry/aurelia-carousel).

## Setup
Run the following commands to create the initial configuration. I like to keep [jspm](http://jspm.io/) installed locally in the project and then use `.\node_modules\.bin\jspm` to access it:

    npm init
    npm init jspm --save-dev
    .\node_modules\.bin\jspm init

This project will need a number of dependencies and thankfully they're all in the [JSPM Registry](http://kasperlewau.github.io/registry/#/) already so we can use their shortnames:

    jspm install aurelia-framework
    jspm install aurelia-bootstrapper
    jspm install bootstrap
    jspm install font-awesome
    jspm install css

We now need some ground-work so add the following code to `index.html` which gets jspm up and running.:

    <!DOCTYPE html>
	<html>
	<head>
		<meta charset="utf-8" />
	</head>
	<body aurelia-app>	
		<script src="jspm_packages/system.js"></script>
		<script src="config.js"></script>
		<script>
			System.import("aurelia-bootstrapper");
		</script>
	</body>
	</html>

By default Aurelia is going to look for an `App` page so we'll need the following in `app.js`:

	export class App {		
	}

More importantly we'll need this template in `app.html`:

	<template>
		<h1>An Aurelia Carousel Component</h1>
	</template>
	
With the initial setup done run the following to install a HTTP server which will monitor the project for file changes and re-load automatically:

    npm install live-server -g
    live-server

### Enabling ES6/7 Functionality
The view model is going to use some ES6/7 features but the transpiler wired in by jspm does not provide these by default. Therefore add the following lines to `babelOptions : { "optional": [ .... ] }`:

      "es7.decorators",
      "es7.classProperties"

		
## Basic Carousel Template
Let's sort out the template first. I basically need a container within which I'll have a left and right button and the content to change. Here's the content for `carousel.html`:

	<template>
		<div class="well carousel">
			<a class="carousel-nav previous">
				<i class="fa fa-arrow-circle-o-left"></i>
			</a>
			<a class="carousel-nav next">
				<i class="fa fa-arrow-circle-o-right"></i>
			</a>
			<ol>
				<li>
					<p>This is the content item 1</p>
				</li>
				<li>
					<p>This is the content item 2</p>
				</li>
				<li>
					<p>This is the content item 3</p>
				</li>
				<li>
					<p>This is the content item 4</p>
				</li>
			</ol>
		</div>
	</template>	

This won't do anything until we have the view model `carousel.js`:

	export class CarouselCustomElement {
	}

This doesn't look like much but I'm using the `CustomElement` suffix on the class name which, by convention, identifies this as an HTML element that will be rendered by the Aurelia framework.

The carousel renders but it doesn't look like much at the moment. In fact, it looks just like an ordered list of paragraphs! We'll change that now by adding some style.

## A Little Style
The template above is using Bootstrap class of `well` and font-awesome icons too so we'll need to bring those dependencies in. Add the following to `carousel.js`:

	import "font-awesome";
	import "bootstrap/css/bootstrap.css!";

If I required any of the active components in Bootstrap then I would also need `import "bootstrap";` but for the moment that's left off. With the two imports above the carousel now has a border and background (the well) and we can see the left/right arrows.

To customise this further add `carousel.css` and import it in `carousel.js` with `import "./carousel.css!";`. 

Just a quick note on the import syntax that I've used twice now - this is using a [jspm plugin](https://github.com/systemjs/plugin-css) which was installed with the command `jspm install css` earlier and allows css files to be imported with a trailing exclamation mark which triggers the plugin functionality - in this case the plugin injects a `<link rel="stylesheet">` referencing the css file.

### Navigation Buttons
Now let's tackle the navigation buttons which will move the carousel to the next and previous item and in this code, will wrap at the end. For the moment we'll just style the buttons and come back to the functionality later.

Add the following to `carousel.css`:

	.carousel .carousel-nav {
		z-index: 1; /* Just enough to edge this above the content */
		position: absolute;
		top: 1.5em;
		overflow: hidden; /* Don't show the <li> that we're pushing down
	}

	.carousel .carousel-nav .fa {
		font-size: 4em;
		color: lightgray;
	}

	.carousel .carousel-nav .fa:hover {
		color: gray;
		cursor: pointer;
	}

	.carousel .carousel-nav.previous {
		left: 1em;
	}

	.carousel .carousel-nav.next {
		right: 1em;
	}

This simply resizes and colours the buttons then with `absolute` positioning attaches them to the left and right edges.

### Content
The way I'm making this carousel work is by pushing all the `<li>` below the carousel container then bringing the current one back to the top. Here's the css:

	.carousel ol {
		list-style: none;
		margin: 0;
		padding: 0;
	}

	.carousel li {
		margin: 0;
		padding: 0;
		height: 7em;
		position: absolute;
		top: 7em;
		width: 100%;
	}

	.carousel li.current {
		top: 0;
	}

Half of this code changes the style of the `<ol>` and `<li>` so they fill the space but the most important aspect are the `top` selectors which pushes all `<li>` elements down by `7em` but brings back the one marked with the `current` class with a `top: 0;`.

At this point your can see how `current` works by adding `class="current"` to any of the `<li>` elements in `carousel.html`.

## Bound Items List
The next job is to provide the list of items for the carousel to display in a bindable items list. So in `app.js` we define the items we want to display:

	items = [
		{"text": "This is the first item"},
		{"text": "This is the second item"},
		{"text": "This is the third item"}
		];

>Note that I'm assuming each item is an object itself. This is a simplification for my purposes but a more complete carousel might allow an array of strings or image filenames and adapt it's functionality based on this input.

In `app.html` we bind that list to the carousel:

	<carousel items.bind="items"></carousel>

But currently the carousel doesn't have the `items` custom attribute so we'll create that using `@bindable` in `carousel.js`:

	import {bindable} from "aurelia-framework";
	export class CarouselCustomElement {
		@bindable items;
	}

Then we can replace all the `<li>` elements in `carousel.html` with a `repeat-for`:

	<li repeat.for="item of items">
		${item.text}
	</li>

>I don't like the reference to `.text` inside the carousel since this means the carousel must understand the content of the objects being bound to but I'll deal that later with templating.

## Display Initial Current Item
Now we want to change the carousel so it displays the first item in the bound list when it's first displayed. Add the following code to `carousel.js`:

		activate() {
			this.itemsChanged(this.items);
		}
	
		itemsChanged(items) {
			this.currentIndex = 0;		
			this.items[this.currentIndex].carouselCurrent = true;
		}

This employs the `activate` event which will be called when the carousel is initialised and this calls the `this.itemsChanged` which is, by convention, the event that will be called when the `@bindable` `items` property is ever modified. I'm creating `this.currentIndex` to track the currently selected item and temporarily setting the current item. 

>I'm being a little naughty here since I'm using the items model passed to me to set a new property called `carouselCurrent` on it. This is fine for me and, in fact, it's potentially useful to bind other functionality off it but I might re-think this in the future.

This still won't work until the view sets the `current` class for the current carousel item so change the `<li>` to this:

	<li repeat.for="item of items"
		class.bind="item.carouselCurrent ? 'current' : ''">
		${item.text}
	</li>

The first item should now be visible in the carousel. 

>Don't worry about the terrible item formatting for the moment. I'll come back to that when I talk about templating.

## Changing the Current Item
It's time to get to core functionality now and make this carousel change the selected item. So let's go right ahead and set bindings on the navigation buttons to trigger functionality in the view model. Make this change in `carousel.html`:

	<a class="carousel-nav previous" click.trigger="previous()">
		<i class="fa fa-arrow-circle-o-left"></i>
	</a>
	<a class="carousel-nav next" click.trigger="next()">
		<i class="fa fa-arrow-circle-o-right"></i>
	</a>

The change here is to add `click.trigger` bindings which will call the following added to the `carousel.js` view model:

	previous() {
		this.navigate(-1);
	}

	next() {
		this.navigate(1);
	}	

And the new method called `navigate` which will move the current item back and forward:

	navigate(direction) {
		if (!this.items[this.currentIndex])
			return;

		this.items[this.currentIndex].carouselCurrent = false;
		this.currentIndex += direction;
		if (direction === -1 && this.currentIndex < 0) {
			this.currentIndex = this.items.length - 1;
		}
		if (direction === 1 && !this.items[this.currentIndex]) {
			this.currentIndex = 0;
		}
		this.items[this.currentIndex].carouselCurrent = true;
	}

Also change `itemsChanged` to this:

	itemsChanged(items) {
		this.currentIndex = 0;		
		this.navigate(0);
	}

I'll not go through this code but suffice to say it increments the `currentIndex` based on direction and wrapping at the ends of the `items` array.

## Adding a Bit of Animation
This isn't a tutorial in css animation but that transition is a little boring so adding the following to `carousel.css` to make it slightly better:

	/* Animation */

	.carousel li {
		opacity: 0;
		transform: scale(0);
		transition: 0.5s;
	}

	.carousel li.current {
		opacity: 1;
		transform: scale(1);
		transition: 0.5s;
	}

## Announcing the Carousel Changed
This carousel will end up being placed within a page or another component and this host may well be interested in knowing when the carousel has changed item!

>The following code might need some more work if more than one carousel is required but this was just enough for my purposes. In particular I would probably look to some sort of property binding solution in the future since that would employ less code and wouldn't broadcast the message to anything that's listening!

For this I'm using an event which anything can listen to. In `carousel.js` I've importing the Aurelia EventAggregator the `publish()` the current selected item from the `navigate` method. Here are just the changes made to `carousel.js`:

	import {EventAggregator} from "aurelia-event-aggregator";
	
	@inject(EventAggregator)
	export class CarouselCustomElement {
		constructor(eventAggregator) {
		    this.eventAggregator = eventAggregator;		
		}
	
		navigate(direction) {
			....
	
			this.eventAggregator.publish(
				'carousel-navigate', 
				this.items[this.currentIndex]);
		}
	}

## Templating
The last major problem to overcome is the formatting of the item that's being displayed. It's not up to the carousel to determine this so somehow the hosting component needs to pass in a template.

I've chosen a syntax that looks a little like `<compose>` and the modification made to `app.html` looks like this:

	<carousel 
		items.bind="items"
		template="./carousel-item.html">
	</carousel>

Here I've added a `template` attribute and pass it a path to a html template.

For the moment I'm keeping the template very basic indeed. Here's `carousel-item.html`:

	<template>
		<require from="./carousel-item.css!"></require>
		<div class="carousel-item">
			<h1>${text}</h1>
		</div>
	</template>

And it's corresponding `carousel-item.css`:

	.carousel-item h1 {
		text-align: center;
	}

In `carousel.js` I define `@bindable template;` but replace the temporary `${item.text}` in `carousel.html` with:

	<compose 
		view="${$parent.template}" 
		view-model.bind="item"/>

So I'm leveraging `<compose>` by handing it the reference to the template from the carousel but also handing it a view model which is the item itself.

By introducing the template the carousel is now left knowing nothing of the content it's asked to display leaving the host to define that instead.

## Summary
This post described how to create a carousel control from scratch for use with Aurelia. It's a fairly opinionated implementation but the groundwork is there for adapting to other scenarios. If you have any comments please do leave them below.