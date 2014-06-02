---
author: Philip Hendry
title: AngularJS Directive Techniques Learnt from a TinyMCE Directive
date: 2014-06-02 13:42:14+00:00
comments: true
layout: post
tags:
- angularjs
---
I've been looking at a [TinyMCE](https://github.com/angular-ui/ui-tinymce/blob/master/src/tinymce.js) directive that made me realise there are a number of techniques used there that would be useful to document. The directive is a great example of producing clean interfaces between AngularJS and other controls.

So, in no particular order, here are the techniques:

### Configuration Options as a JSON Object

There are a number of techniques for passing options to the directive including adding attributes to the HTML element the directive is declared on. However, this particular technique wants to pass a JSON object through to the directive therefore it's passed as the parameter to the directive:

```
<textarea ui-tinymce="theoptions" />
```

These options can be set in a controller:

```
function aController($scope) {
    $scope.theoptions = {
        menubar: false,
        statusbar: false
    };
}
```

and then accessed from within the directive:

```
if (attrs.uiTinymce) {
    expression = scope.$eval(attrs.uiTinymce);
} else {
    expression = {};
}
```

### Configuration Option Overrides

Taking that technique above further, the settings themselves can be defined in multiple areas and, using `angular.extend()`, merged together. The options are:

* Specify the options on the scope in a controller (see above)
* Fixed options within the directive itself. In the directive this was used to hook in callbacks from TinyMCE itself.
* Provide some default options within the directive itself:

```
angular.module('ui.tinymce', [])
       .value('uiTinymceConfig', {
            menubar: false,
            statusbar: false
       });
```


Finally the options are merged and passed to TinyMCE:

```
angular.extend(options, uiTinymceConfig, expression);
setTimeout(function() { 
    tinymce.init(options); 
});
```

### Automatically Generating Ids

This is the first time I'd seen this technique - TinyMCE was used being asked to configure itself against textarea elements with specific IDs. However, if the element with the directive does not have one, one will be generated:

```
if (!attrs.id) {
    attrs.$set('id', 'uiTinymce' + generatedIds++);
}
```

### Updating the View from a TinyMCE Callback

This technique defines a function that is called from the TinyMCE callbacks:

```
updateView = function() {
    ngModel.$setViewValue(elm.val());
    if (!scope.$root.$$phase) {
        scope.$apply();
    }
}
```

It's using [&dollar;setViewValue](https://code.angularjs.org/1.2.0-rc.3/docs/api/ng.directive:ngModel.NgModelController#$setViewValue) to write the value from TinyMCE back on to the model. It's drummed in to you that calling &dollar;apply() is necessary after updating the model but what I've not seen much of is the call to `scope.&dollar;root.&dollar;&dollar;phase` that checks that we aren't already in the digest loop. Given AngularJS is going to keep processing the digest loop until everything is gone the model update will be picked up.

### Hooking into the View Update

Given this directive takes over the rendering of content it must hook into the render request from AngularJS. [&dollar;render](https://code.angularjs.org/1.2.0-rc.3/docs/api/ng.directive:ngModel.NgModelController#$render) does exactly that and in ui-tinymce it's used to copy the data from the model into the TinyMCE control:

```
ngModel.$render = function() {
    if (!tinyInstance) {
        tinyInstance = tinymce.get(attrs.id);
    }
        if (tinyInstance) {
            tinyInstance.setContent(ngModel.$viewValue || '');
    }
};
```

### Destroying Content

Finally the normal request to the scopes [&dollar;on](https://code.angularjs.org/1.2.0-rc.3/docs/api/ng.$rootScope.Scope\$on) method to respond to the request to &dollar;destroy the control:

```
scope.$on('$destroy', function() {
    if (!tinyInstance) { tinyInstance = tinymce.get(attrs.id); }
    if (tinyInstance) {
        tinyInstance.remove();
        tinyInstance = null;
    }
});
```