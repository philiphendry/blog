---
author: Philip Hendry
title: Integrating JSPM and Aurelia in Visual Studio
date: 2015-10-20 14:00
comments: true
layout: post
tags:
- aurelia
- javascript
- asp-net
- visual-studio
---
My [last post](http://blog.philiphendry.me.uk/2015/10/18/bundling-with-jspm-and-aurelia/) concentrated on looking at how an Aurelia app is bundled. I intentionally kept things very simple so now I want to tackle some of the detail.

In particular, this post will investigate how to integrate the building and bundling of my Aurelia app inside an existing ASP.NET WebForms/MVC application. Here are some things I need to keep in mind:

* What folder structure should I use?
* I need to continue debugging the Aurelia app but can I do that with the bundle and source maps or will I need a bundled release version and a unbundled development version?
* How will I be running gulp? Integration with Visual Studio and the build process will be nice!
* My release builds run on TFS Team Build and for deployment I'm using [Octopus Deploy](https://octopusdeploy.com/) so I'll need to integrate with those.

This blog is being written as I investigate to it might be a bit wordy at times but hopefully the journey will prove interesting!

## Folder Structure

My Visual Studio project folder structure currently looks like this:

```
  Solution/
  ├── Build/                      Files required for the VS build
  ├── ...
  ├── GUI
  │   └── Web Project (.csproj)
  │      ├── App/                 File containing an AngularJS 1.x SPA
  │      ├── App_Code/            A special ASP.NET folder
  │      ├── Controllers/         WebAPI controllers delivering JSON to the SPA
  │      ├── Scripts/             A folder for Javascript files
  │      └── web.config
  ├── ...
  └── Solutions Items/
```

This is obviously highly stylised and I've omitted lots of other folders but the ones above should prove useful and at the least provides a framework for discussing below.

My first decision is whether to create a completely separate application for the Aurelia SPA or just include all the necessary files in this current project. I've opted for the latter since the rest of the Web Project will migrate to using Aurelia in the future and hopefully removing all the WebForms and most of the ASP.NET MVC code. Development should be possible either within Visual Studio (TypeScript support is excellent and I might take this route in the future) but currently I'm using Sublime Text so having a sub-folder for the Aurelia would seem appropriate since I won't want to edit anything else in Sublime!

I'll take a wild stab at what I expect the Aurelia code to looks at and expect to have this:

```
  Solution/
  ├── Build/                      
  ├── ...
  ├── GUI
  │   └── Web Project (.csproj)
  │      ├── App_Code/            
  │      ├── build/               Gulp specific javascript and tasks
  │      │   └── tasks/            
  │      ├── Dist/                The Aurelia app builds into here
  │      ├── Controllers/         
  │      ├── jspm_packages/       Never to be checked in
  │      ├── node_modules/        Never to be checked in
  │      ├── Scripts/             
  │      │   └── bundle.js        The Aurelia bundle of javascript etc       
  │      ├── Src/                 The Aurelia source goes here
  │      ├── gulpfile.js
  │      ├── index.html           The entry-point for the Aurelia app
  │      └── web.config
  ├── ...
  └── Solutions Items/
```

I'll create the `bundle.js` in the `Scripts/` folder since it feels in-keeping with the structure I've been used to so far. The `Dist/` and `Src/` folders won't be deployed - I did consider putting both of these folder in `App_Code/` since that's special folder that ASP.NET will never deliver to the browser but for some reason I'm try not to lean on anything in ASP.NET!

## Configuring Gulp

I'm going to modify a project I [added to github](https://github.com/philiphendry/bundling-with-jspm-and-aurelia) for my previous blog and change this to mirror the folder structure I want along with having the gulp build tasks I'll need too. I'll also plan on wrapping it in a Visual Studio project so I can test the integration.

My first change to this project is to move the files around into the following:

```
  bundling-with-jspm-and-aurelia/
  ├── src/                      
  │   └── app.html
  │   └── app.js
  │   └── main.js
  ├── jspm_packages/
  ├── node_modules/
  ├── config.js
  ├── gulpfile.js
  ├── index.html
  └── package.json
```

This broke the app since the `System.import("main");` in `index.html` can't find `main` anymore. However, this is easily fixed by letting SystemJS know where to look for modules in the `config.js`:

    paths: {
      "*": "src/*",
      "github:*": "jspm_packages/github/*",
      "npm:*": "jspm_packages/npm/*"
    }

The new line `"*": "src/*"` tells SystemJS that to find anything look in `src/` unless there's a more specific matching path.

If I now run `gulp bundle` I discover the site is broken which is to be expected since that bundling is generating `build.js` in the root whilst the path configuration above will look for `src/build.js`.

There are a couple of changes to make here. The first is in the `gulpfile.js` which now looks like this:

    var gulp = require("gulp");  
    var bundler = require("aurelia-bundler");

    var config = {  
      "force": true,
      "packagePath": ".",
      "bundles": {
        "dist/build": {
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
          "minify": true,
          "sourceMaps": true
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

The change is naming the bundle `"dist/build": { ... }` instead of `"build": { ... }` so that the `build.js` is placed in the `dist` folder. I've also taken this chance to add `"sourceMaps": true` so `build.js.map` is generated along side it.

The second change is to change `config.js` and swap the path `"*": "src/*"` for `"*": "dist/*"`! Obviously I've just changed this once already but this now makes a lot more sense even more sense when there's a gulp watch and build to automatically keep `dist` up-to-date with the latest development changes which are subsequently delivered from the `dist` folder.

## Integrating with Visual Studio

I'm going to create a Visual Studio project now and pull in the files so I'll spin up VS and select `File -> New Project` and select the `ASP.NET Web Application` template. The name is `bundling-with-jspm-and-aurelia` whilst the location is the parent folder so `c:\github` - doh, but VS won't let me do that so I'll create it in a new folder and copy over what I need.

From the Web templates I'll simply create the Empty ASP.NET 4.5.1 Template - it will be interesting to see how the ASP.NET 5 Preview Template might help me here but I'm integrating into an existing project so that's another blog :)

Once created I grabbed everything in the project folder (`Properties/`, `bundling-with-jspm-and-aurelia.csproj`, `packages.config`, `web.config`, `web.debug.config` and `web.release.config`) and dropped it in the root of my 
`bundling-with-jspm-and-aurelia` folder.

In Visual Studio I've reloaded the project, selected `Show all files` from the `Solution Explorer` toolbar then included all my project files (but not `jspm_packages/` or `node_modules/` - I made that mistake and it's painful!)

Here's how my solution explorer looks:

![Solution Explorer](/assets/2015/integrating-jspm-and-aurelia-in-visual-studio-solution-explorer.PNG "Solution Explorer")

### Adding Build Tasks

At this point I'm going to need a build gulp task to compile everything from `src/` into `dist/` so I can then bundle. 

> I'm undecided whether to have an intermediate folder for the build since this might make it easier to take a single folder containing bundles and pull/link it into Visual Studio. So for example I develop in `src/`, build to `jsbuild/` then bundle into `dist/`. I'll come back to this later.

I'm going to use the [build.js](https://raw.githubusercontent.com/aurelia/skeleton-navigation/master/build/tasks/build.js) from the Aurelia Skeleton app and put this into `build/tasks/` which seems to be a fairly standard folder stucture now. This means I've also copied the current contents of `gulpfile.js` and moved it to `build/tasks/bundle.js` and replaced it with:

    require('require-dir')('build/tasks');

This means I'll also need to install `require-dir`:

    > npm install require-dir run-sequence gulp-changed gulp-plumber gulp-babel gulp-sourcemaps gulp-notify vinyl-paths del object.assign --save-dev

There are a lot of packages to install since they're all `require()'d` by the gulp tasks. I've also had to copy across `clean.js`, `babel-options.js` and `paths.js` from Aurelia Skeleton.

However, once all that was done I could run the following:

    > gulp build
    > gulp bundle

And I end up with a `dist/` folder full of my built files but also `build.js` and `build.js.map` which are the bundled files I'll need ultimately.

### Running Gulp Tasks in Visual Studio

Visual Studio 2015 now includes the _Task Runner Explorer_ which used to be an extension. This automatically finds `gulpfile.js` and interprets its content given me the following options:

![Visual Studio 2015 Task Runner Explorer](/assets/2015/visual-studio-2015-task-runner-explorer.PNG "Visual Studio 2015 Task Runner Explorer")

At this point I can right-click any of the tasks and run them with output displayed in the runner. However, even more useful is the ability to bind the tasks to events in Visual Studio:

![Task Runner Explorer Task Binding](/assets/2015/visual-studio-2015-task-runner-explorer-binding-options.PNG "Visual Studio 2015 Task Runner Explorer Task Binding Options")

But I'm stuck between choosing the obvious and more convenient! The obvious would be to bind the `build` task to the `After Build` event. But the more convenient would be to launch a `watch` on `Project Open` so I have browser-sync keeping my browser up-to-date.

I've gone for the latter option although it took me a google search to find out why Visual Studio wasn't persisting my choice! When you change the bindings Visual Studio puts a comment at the top of `gulpfile.js` but it didn't save it when I close the solution and reloaded. Therefore, make sure `gulpfile.js` looks like the following and it's saved:

    /// <binding ProjectOpened='watch' />
    require('require-dir')('build/tasks'); 

Now whenever I make a change to the html, css or javascript the code is compiled immediately providing instant feedback. I might even be able to connect up browser sync but I'm planning on having the site delivered through IIS Express at this point.

> In order for the development build to be delivered correctly the app must be unbundled first since at the moment `gulp watch` cleans the `dist/` folder which means `config.js` will point to a `build.js` bundle that no longer exists!

### Integrating with TFS Team Build

At this point I'm going to want the `build` and `bundle` tasks run on the build server along with `npm install` and `jspm install -y` which are required to restore all the packages.

This is actually surprisingly easy. I'm adamant that changing the TFS build process templates is a terrible idea since debugging them is slow and laborious. Instead I prefer to integrate into the project msbuilds allowing everything to be tested and debugged locally before checking into source control. It also means we could move from TFS builds to another build server with very little effort if required.

The first step is to create a file called `SpaBuild.msbuild` which will contain all the logic to build our app:

    <Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">

        <!-- Hook into the AfterBuild activity -->
        <PropertyGroup>
          <BuildDependsOn>
              $(BuildDependsOn);
              SpaBuild;
              SpaBundle
          </BuildDependsOn>
        </PropertyGroup>

        <PropertyGroup>
          <RunSpaBuild Condition="'$(RunSpaBuild)'==''">false</RunSpaBuild>
          <RunSpaBundle Condition="'$(RunSpaBundle)'==''">false</RunSpaBundle>
        </PropertyGroup>

        <Target Name="SpaBuild" Condition="$(RunSpaBuild)">
          <Message Text="Building the SPA application" />
          <Exec Command="attrib -r config.js" WorkingDirectory="." />
          <Exec Command="attrib -r package.json" WorkingDirectory="." />
          <Exec Command="npm install" WorkingDirectory="." />
          <Exec Command="jspm install -y" WorkingDirectory="." />
          <Exec Command="gulp build" WorkingDirectory="." />
        </Target>

        <Target Name="SpaBundle" Condition="$(RunSpaBundle)">
          <Message Text="Bundling the SPA application" />
          <Exec Command="attrib -r config.js" WorkingDirectory="." />
          <Exec Command="attrib -r package.json" WorkingDirectory="." />
          <Exec Command="gulp bundle" WorkingDirectory="." />
        </Target>

    </Project>

I won't go into too much detail here since it's fairly self-explanatory. However here are a few bullet points:

* The `attrib` commands are required because TFS checks out source but with the read-only flag set and the build process will ultimately overwrite them. 
* The `WorkingDirectory` (which really should be pulled out as a variable) is the root folder containing the single page appliation code.
* The `SpaBuild` and `SpaBundle` targets are turned on/off with `RunSpaBuild` and `RunSpaBundle` properties and are disabled by default.

This build script is the imported into any required _csproj_ file with the following code:

    <Import Project="SpaBuild.msbuild" />

Now the spa build and bundle can be run at the command-line:

    > msbuild bundling-with-jspm-and-aurelia.csproj /p:RunSpaBuild=true /p:RunSpaBundle=true

These parameters can then be plugged into the TFS Build Definition as arguments to the MSBuild:

![TFS Build Definition MSBuild arguments for SPA build and bundle](/assets/2015/tfs-build-definition-msbuild-arguments-for-spa-build-and-bundle.PNG "TFS Build Definition MSBuild arguments for SPA build and bundle")

### Bundling into Deployment with Octopus

This blog post has gone on far too long so I'm going to cheat and simply say that Octopus leverages nuget project files and I will probably add my own customised one that pulls the bundled files in leaving all the otherwise behind.

## Conclusion

The final integration into Visual Studio feels pretty seamless and given the `watch` task launches automatically when I load the project there's simply nothing left to do. Admittedly I'm not planning on developing against the bundled version but that seems fair enough and I've always got the option to run the bundle manually and launch my browser.

The source for everything can be found in [github](https://github.com/philiphendry/bundling-with-jspm-and-aurelia/)