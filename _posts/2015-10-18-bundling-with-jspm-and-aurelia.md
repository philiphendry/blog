---
author: Philip Hendry
title: Bundling with JSPM and Aurelia
date: 2015-10-18 20:30
comments: true
layout: post
tags:
- aurelia
- javascript
---
I've been working on integrating Aurelia with my current Visual Studio ASP.NET WebForms/MVC application and so far things have been going well. And then I tried to deploy! The problem is I want to deploy to our test servers the version of the Aurelia app that doesn't try and download over 200 files on the first page render!

Enter bundling! I've successfully bundled before and my current favoured approach is to use [Web Essentials](http://vswebessentials.com/) because

* I like the simplicity in how things are configured
* it works during building a project and that means it works on the build server too
* it gives good feedback when bundling goes wrong. 
 
It's this last point that drove me away from Microsofts bundling since when it found syntax errors in the CSS during bundling it simply included the unbundled version and carried on with the rest - leaving me to discover our live server delivering unbundled CSS whilst nosing around one day!!!

## JSPM & Aurelia

But now that I'm using Aurelia with ES6 transpiling I need something to bundle my app a little more intelligently. And initially that's where [JSPM](http://jspm.io/) comes in. [Aurelia](http://aurelia.io/) uses JSPM to deliver package management but also the module loading project by [SystemJS](https://github.com/systemjs/systemjs).

There's a confusing plethora of technologies going on here and whilst I've followed [Bundling Aurelia Apps](http://blog.durandal.io/2015/09/11/bundling-aurelia-apps/) in the past and successfully bundled my app, I've never fully appreciated what was going on. Now that things are broken :) I need to take a step back and understand things a little better.

So here I'm am about to build a simple app from scratch and create a bundle using the [jspm-cli](https://github.com/jspm/jspm-cli/blob/master/docs/production-workflows.md) documentation. I'll be writing this blog as I create the app so there may be some mistakes along the way - but that's part of the process I wanted to follow.

## Basic App

First step then is to create a very, very basic app view it then see what difference JSPM bundling makes. So let's run a few commands to create this app:

    > md bundling-with-jspm-and-aurelia
    > cd bundling-with-jspm-and-aurelia
    > npm init    

For the moment I'll just accept all the defaults when running `npm init`.

    > npm install jspm --save-dev

It's important to have reproducable builds so I always follow the recommendation of installing jspm locally (as well as globally too.)

    > jspm init

I'm going to accept all the defaults although I might come back to review how the `baseURL` affects the bundling - I won't pretend to fully understand what this does!

I need an entry point so I'll create `index.html` as shown below:

    <!doctype html>
    <head>
      <script src="jspm_packages/system.js"></script>
      <script src="config.js"></script>
      <script>
        <!-- imports --->
      </script>
    </head>
    <body>
      <h1>Bundling with JSPM and Aurelia</h1>
    </body>

At this point I can spin up [live-server](https://www.npmjs.com/package/live-server) and check everything is downloading fine.

## Add a Dependency

So far this is proving nothing since there's nothing really to bundle. So I'll import bootstrap for a bit of style:

    > jspm install bootstrap

The browser still won't be downloading anything new until we use this dependency so I'll following script to `index.html` (I've put a comment above to highlight where this import will go):

    System.import("bootstrap");

When I look at the network tab in Chrome I see the list of files grow from just `index.html`, `system.js` and `config.js` to now include the following:

    bootstrap@3.3.5.js
    bootstrap.js
    jquery@2.1.4.js
    jquery.js

`jQuery` is a dependency of `bootstrap` therefore it's also been included. The version file contains the module import for the specific version so, for example, `bootstrap@3.3.5.js` contains the following:

    module.exports = require("github:twbs/bootstrap@3.3.5/js/bootstrap");

## JSPM Bundling

Now I have something to bundle... at least I thought I did! Checking the [documentation](https://github.com/jspm/jspm-cli/blob/master/docs/production-workflows.md) shows me that I'll need to point the jspm bundling at a javascript file. Therefore I'll adopt the standard of creating a `main.js` and import that instead from the `index.html`. Here's `main.js`:

    import "bootstrap";

and I'll change the import in `index.html` to look like this:

    System.import("main");

Given that I'm using ES6 syntax in `main.js` now SystemJS is clever enough to realise the browser is going to need to transpile this into ES5 and therefore the Chrome network tab shows `babel-core@5.8.25.js` and `browser.js` being loaded.

I can now run:

    > jspm bundle main

All this command does is generate a file called `build.js` and another `build.js.map` which contains all the files necessary javascript. To actually make a difference I need to run this instead:

    > jspm bundle main --inject

This time the browser only shows me the following files being loaded:

* index.html
* system.js
* config.js
* build.js

What's happened is jspm has added the following to the `config.js` which is telling SystemJS that what it needs can be found in `build.js`:

    bundles: {
      "build.js": [
        "main.js",
        "github:twbs/bootstrap@3.3.5",
        "github:twbs/bootstrap@3.3.5/js/bootstrap",
        "github:components/jquery@2.1.4",
        "github:components/jquery@2.1.4/jquery"
      ]
    }

I can also see that the `bundle.js` has transpiled by `main.js` from ES6 into ES5 removing the need to send babel to the browser.

### Bundling CSS

At this point I'm a little curious as to how non-javascript files will be bundled so I'll try a little experiment and get `bootstrap.css` included in the bundle.

I'll revert my site to the unbundled version with:

    > jspm unbundle

Then I'll need to add the [css plugin](https://github.com/systemjs/plugin-css) which can be used to import css with SystemJS:

    > jspm install css

Then in `main.js` I'll import the css:

    import "boostrap/css/bootstrap.css!";

What I need to remind myself here is the first `boostrap/` in the import maps to `/jspm_packages/github/twbs/bootstrap@3.3.5/` then the remainder is beneath that path!

When the browser refreshes I can now see `css.js` loading (the CSS plugin) and `bootstrap.css` which is what I'm actually after. I can also see the heading is now bootstrap-styled so everything is working.

Now let's bundle again:

    > jspm bundle main --inject

But that leads to the following error:

    err  Error: Install Clean CSS via jspm install npm:clean-css for CSS build support. Set System.buildCSS = false to skip CSS builds.
             at file:///C:/github/bundling-with-jspm-and-aurelia/jspm_packages/github/systemjs/plugin-css@0.1.18/css.js:86:13

I'll do as the error suggests and run:

    > jspm install npm:clean-css

This time running the bundling command works and I see the browser just loading `build.js` but more importantly there are no `.css` files being loaded whilst the bootstrap style is still being applied.

### Bundling Everything

The bundling so far still has `index.html` reference and download `system.js` and `config.js` but it is possible to just download one file. I've created a new page called `index-sfx.html` (*sfx* for self executing) and added the following:

    <!doctype html>
    <html>
    <head>
      <meta charset="utf-8" /> 
      <script src="build-sfx.js"></script>
    </head>
    <body>
      <h1>Bundling with JSPM and Aurelia</h1>
    </body>
    </html>

Then run the following command:

    > jspm bundle-sfx main build-sfx.js

And that's it! Navigating to `http://127.0.0.1:8080/index-sfx.html` yields just two downloads - `index-sfx.html` and `build-sfx.js`.

### Minifying

Before moving on to Aurelia application bundling there's the final issue of minifying all the bundle content. This turns out to be a simple as adding `-m` to the bundle command which becomes:

    > jspm bundle-sfx main build-sfx.js -m

In my environment the `build-sfx.js` file went from 401kb to 243kb which is a healthy saving which the likes of `gzip` will help even further.

## Aurelia Bundling

Now let's add a little Aurelia code and test bundling using jspm. Following that I'll try the `aurelia-bundler` gulp task and see how well that works.

### Add Some Aurelia

I'll need to bootstrap Aurelia so first I'll install `aurelia-bootstrapper`:

    > jspm install aurelia-bootstrapper

then swap the `main` import in `index.html` for:

    System.import("aurelia-bootstrapper");

The browser refreshes and nothing is broken... although there are a lot more components being downloaded now!

I'm going to add a view and view model now lifted straight from the Aurelia getting started. Here's `app.html`:

    <template>
      <section>
        <h2>${heading}</h2>
        <form submit.delegate="submit()">
          <div>
            <label>First Name</label>
            <input type="text" value.bind="firstName">
          </div>
          <div>
            <label>Last Name</label>
            <input type="text" value.bind="lastName">
          </div>
          <div>
            <label>Full Name</label>
            <p>${fullName}</p>
          </div>
          <button type="submit">Submit</button>
        </form>
      </section>
    </template>

and `app.js`:

    export class Welcome{
      heading = 'Welcome to the Aurelia Navigation App!';
      firstName = 'John';
      lastName = 'Doe';
      get fullName(){
        return `${this.firstName} ${this.lastName}`;
      }
      submit(){
        alert(`Welcome, ${this.fullName}!`);
      }
    }

This isn't going to show yet until the `aurelia-app` is connected in the `index.html` so the `body` element now looks like this:

    <body aurelia-app>

This time the browser errors with the following:

    Error: http://127.0.0.1:8080/app.js: Unexpected token (2:10)
      1 | export class Welcome{
    > 2 |   heading = 'Welcome to the Aurelia Navigation App!';
        |           ^
      3 |   firstName = 'John';
      4 |   lastName = 'Doe';
      5 | 
        Error loading http://127.0.0.1:8080/app.js

This happens because the Aurelia getting started code is using and ES7 feature which I haven't turned on. So back in `config.js` I change the `babelOption` to include two new ES7 features:

    babelOptions: {
        "optional": [
          "runtime",
          "optimisation.modules.system",
          "es7.decorators",
          "es7.classProperties"      
        ]
      }

The browser refreshes and the view is displayed.

I've noticed I've lost the bootstrap css and I'd like that to be a part of the bundling Aurelia test. The reasons it's gone is I swapped the import of `main.js` for the `aurelia-bootstrapper` so I'll just add the following line to the top of `app.js`:

    import "bootstrap/css/bootstrap.css!";

The `<h1>` is now styled again.

### Bundling Aurelia with JSPM directly

First attempt I try this:
    
    > jspm bundle app --inject

This creates a `build.js` but there are still loads of other modules being loaded and I presume this is due to the `aurelia-bootstrapper` import left in `index.html`.

After some trial-and-error I ended up running the following two commands:

    >jspm install aurelia-bootstrapper aurelia-framework aurelia-dependency-injection aurelia-router aurelia-loader-default aurelia-history-browser aurelia-templating-router aurelia-logging-console aurelia-templating-binding aurelia-templating-resources
    >jspm bundle main + app + aurelia-bootstrapper + aurelia-framework + aurelia-dependency-injection + aurelia-router + aurelia-loader-default + aurelia-history-browser + aurelia-templating-router + aurelia-logging-console + aurelia-templating-binding + aurelia-templating-resources --inject

The first installs all the Aurelia components I require and the second generates a bundle but manually includes the components I need.

What I subsequently can't seem to get working is generate the self-executing version with `jspm bundle-sfx`. The bundle is generated but when it loads it errors with:

    Unhandled promise rejection TypeError: define is not a function(…)

I'm going to ignore this for the moment and move on to getting the Aurelia bundler working with gulp.

### Bundling Aurelia with Aurelia-Bundler and Gulp

The [aurelia-bundler](https://github.com/aurelia/bundler) is a gulp task and it's the next stage of my experiments.

To begin I'm going to need gulp (which is already installed globally) and `aurelia-bundler` and I'll also undo the bundling I've create earlier with jspm:

    > jspm unbundle
    > npm install gulp -- save-dev
    > npm install aurelia-bundler --save-dev

I'll create a `gulpfile.js` next with the following content:

var gulp = require("gulp");  
var bundler = require("aurelia-bundler");

    var config = {  
      "force": true,
      "packagePath": ".",
      "bundles": {
        "build": {
          "includes": [
            "main",
            "app",
            "aurelia-framework",
            "aurelia-bootstrapper",
            "aurelia-router",
            "aurelia-loader-default",
            "aurelia-logging-console",
            "aurelia-templating-binding",
            "aurelia-templating-resources",
            "aurelia-templating-router",
            "aurelia-history-browser"
          ],
          "options": {
            "inject": true,
            "minify": true
          }
        }
      }
    };
    gulp.task("bundle", function() {  
     return bundler.bundle(config);
    });
    gulp.task("unbundle", function() {  
     return bundler.unbundle(config);
    });

And then run this with:

    > gulp bundle

Admittedly I've jumped some of the steps I went through here. In particular I ran bundling with a minimal list of includes in the config and added module names for each dependency being loaded in the Chrome network tab. 

This configuration could also be further improved by using pattern matching for application files (`app` and `main`) and possibly splitting out application bundle and an infrastructure bundle. I also have no idea how to create a self-executing bundle similar to the jspm version I created above - but given that one wouldn't work I'm not missing out much this far!

## Source Code

I've upload the source code to [github](https://github.com/philiphendry/bundling-with-jspm-and-aurelia) although I may work on it in the future so be aware it might not look exactly as above. Pull requests gratefully received if you have any good suggestions or best practises I'm missing.

## Conclusion

Wow, that was a trip and a half! I've learnt a far amount and feel far more confident preparing my bundling for production applications. However, there are some remaining areas which I'll return to in the future including:

* A discussion on folder structures and how that influences the configuration of jspm `baseURL` and gulp tasks.
* Fixing the self-executing bundle generation for Aurelia apps.
* Integrating my bundle generation into a Visual Studio ASP.NET WebForm/MVC application including having it built by a TFS build server.