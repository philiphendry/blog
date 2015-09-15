---
author: Philip Hendry
title: Creating a HighCharts Component in Aurelia
date: 2015-09-15 09:00
comments: true
layout: post
tags:
- aurelia
---
This article shows how I've integrated [HighCharts](http://www.highcharts.com/) with an Aurelia application I've be writing. The source code is available on [github](https://github.com/philiphendry/aurelia-highcharts).

## Setup
The setup for this test project I've written to host the chart component is fairly basic and most of the salient points are covered in my other post where I created a [carousel component](http://blog.philiphendry.me.uk/2015/09/13/creating-a-carousel-in-aurelia/).

The one obvious difference is pulling HighCharts into the project. Given that HighCharts has a [github repository](https://github.com/highslide-software/highcharts.com) I can use jspm to pull that repository down and assign it to a shortname:

    .\node_modules\.bin\jspm install highcharts=github:highslide-software/highcharts.com

>I believe there's a way to create an entry in the config.js for jspm that defines exactly what is required from the repository rather pulling all of it down (that's what the [jspm registry](https://github.com/jspm/registry) does) but I've not covered that here.

Don't forget, if you're going to use HighCharts for commercial purposes then [go and get a license](http://shop.highsoft.com/highcharts.html).

## Component API
The first thing to ponder is the API that the host view will use to declare the chart component. This is a fairly simple decision since Custom Elements are the de-facto standard. So this is what my page view will declare:

    <require from="./chart"></require>
    <chart chart-options.bind="chartOptions"></chart>

I could make this interface much more elaborate and have attributes for the title of the graph, the axis labels and so forth. If I were trying to wrap the HighCharts API behind the component then that would be a good idea but in my instance I'm not especially given I don't have many instances of HighCharts at the moment. Therefore I'm keeping it simple and passing the HighCharts object directly to the component.

## Chart Component

The basic structure of this component is fairly normal:

    import {bindable, inject} from "aurelia-framework";
    import {TaskQueue} from "aurelia-task-queue";
    import $ from "jquery";
    import Hightcharts from "highcharts";
    import "./chart.css!";

    @inject(Element, TaskQueue)
    export class ChartCustomElement {        
        @bindable chartOptions;
    }

The constructor is injected with the DOM element representing the `<chart>` and the Aurelia TaskQueue which I'll use and explain next. It's also configures global HighCharts options:

    constructor(element, taskQueue) {
        Highcharts.setOptions({
            global: {
                useUTC: false
            }
        });
        this.element = element;     
        this.taskQueue = taskQueue;
    }

Given the `chartOptions` is defined as `@bindable` I can employ the convention of declaring a `chartOptionsChanged` method which will be called whenever the property changes:

    chartOptionsChanged(value) {
        let newSettings = {};
        $.extend(true, newSettings, this.chartDefaults, value);
        this.taskQueue.queueMicroTask(() => 
            $(this.element).highcharts(newSettings)
        );
    }

There are a couple of points to note:

* I'm using jQuery's `extend` method to do a deep merge of some default HighChart options with the ones passed in. This could be extended in the future to supply different chart configurations within the component.
* The chart must render after the element it's contained so the HighCharts code can measure how big the chart should be. This is why I'm using the `taskQueue.queueMicroTask()`.

>I did have a problem at one point where I ended wrapping the `$(this.element).highcharts(newSettings)` in a call to `setTimeout` which definitely seems unnecessary but I can't get a handle on why this seemed required at the time!

For completeness here are the chart defaults I'm using:

    chartDefaults = {
        exporting: {
            buttons: {
                contextButton: {
                    menuItems: [
                        { 
                            textKey: 'printChart', 
                            onclick: function() { this.print(); } 
                        }, 
                        { separator: true }, 
                        { 
                            textKey: 'downloadPNG', 
                            onclick: function() { this.exportChart(); } 
                        }, 
                        { 
                            textKey: 'downloadJPEG',
                            onclick: function() { 
                                this.exportChart({ type: 'image/jpeg' }); 
                            } 
                        }, 
                        { 
                            textKey: 'downloadSVG', 
                            onclick: function() { 
                                this.exportChart({ type: 'image/svg+xml' }); 
                            } 
                        }
                    ]
                }
            }
        }
    };

The final piece to the puzzle is `chart.css` which ensures the `<chart>` is rendered as a block element and therefore expands to fill it's container:

    chart {
        display: block;
    }

## Summary
There's still a lot I could add to this component and I'm sure I'll be dipping back into the repository to improve it in the future. Please feel free to comment below or fork the repository :)