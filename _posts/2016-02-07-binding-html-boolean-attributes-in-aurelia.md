---
author: Philip Hendry
title: Binding HTML Boolean Attributes in Aurelia
date: 2016-02-07 14:00
comments: true
layout: post
tags:
- aurelia
- javascript
---
I've noticed before how HTML defines attributes such as `multiple` which don't take values but I didn't appreciate that the HTML specification has a term for these - [boolean attributes](https://www.w3.org/TR/html51/infrastructure.html#boolean-attributes). And so I came across this term when I was trying to bind `multiple` in a `<select>` using Aurelia. Take the following Aurelia template as an example:

```
  <select multiple>
    <option repeat.for="item in items">${item.label}</option>
  </select>
```

This is ok but what if I have a view model property called `allowMultiple` which indicates whether `multiple` should be added or not. I can't use `multiple.bind="allowMultiple"` since that will bind the boolean result to the attribute as either `multiple="true"` or `multiple="false"`.

Unfortunately Aurelia doesn't support this ([yet](https://github.com/aurelia/templating/issues/76#issuecomment-178832689)) but it's easy enough to work around.

First create a custom attribute:

```
  import {inject} from "aurelia-framework";

  @inject(Element)
  export class MultipleIfCustomAttribute {

    constructor(element) {
      this.element = element;
    }

    valueChanged(newValue) {
      if (newValue) {
        this.element.setAttribute("multiple", "");
      } else {
        this.element.removeAttribute("multiple");
      }
    }
  }
```

Now bind to the attribute allowing it to control whether `multiple` should appear or not:

```
  <select multiple-if.bind="allowMultiple">
    ...
  </select>
```

I think that's a nice simple approach although I'm hoping Aurelia might adopt something like `multiple.if="allowMultiple"` in the future so I don't have to create a custom attribute for every boolean attribute (though I can't imagine they are many!)