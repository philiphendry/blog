---
author: Philip Hendry
title: Binding Keypresses using Mousetrap in Aurelia
date: 2016-02-07 14:00
comments: true
layout: post
tags:
- aurelia
- javascript
---
I wanted to enhance my Aurelia app with some keyboard shortcuts so immediately searched the internet for a library that would overcome browsers differences for me whilst making it easy to set up key presses and their callbacks.

I fairly quickly found [Mousetrap](https://craig.is/killing/mice) which I instantly liked - love it's URL!

But whilst I quickly configure my keypresses I noticed my Aurelia view models started to look a little messy and had a hard-reference to Mousetrap.

I tried a couple of approaches but gravitated to having a custom element (I'll explain more on that later) and so here's `keyboard-element.js`:

```
  import {inject, customElement, bindable} from "aurelia-framework";
  import MouseTrap from "mousetrap";

  @customElement('keyboard')
  @inject(Element)
  export class KeyboardElement {
    @bindable keycombo = "";
    @bindable onkeypress = function defaultOperation(event, keypress) { };

    constructor(element) {
      this.element = element;
    }

    bind() {
      this.mousetrap = new MouseTrap(this.element);
      this.bindKeyboard();
    }

    unbind() {
      this.mousetrap.reset();
      delete this.mousetrap;
    }

    bindKeyboard() {
      this.mousetrap.reset();
      this.mousetrap.bind(
        this.keycombo,
        (event, combo) => this.onkeypress({ event: event, keypress: combo })
      );
    }
  }
```

This is now the only code that pulls in Mousetrap and provides two properties `keycombo` and `onkeypress` for the consumer to configure:

```
  <keyboard 
      keycombo.bind="keys" 
      onkeypress.call="onkeypress(event, keypress, item)">
    ...
  </keyboard>
```

The consuming view model supplies the keypresses it wants to receive. Given this is passed to Mousetrap the specification of keys is taken right from it's documentation :

```
  keys = ["tab", "shift+tab", "alt+h", "alt+del"];
```

Initially I had tried using a custom attribute to define the key presses but I found it difficult to define a callback within which `this` was appropriately set - after all, these key presses will occur in a particular HTML context from which I want to extract information. 

To explain what I mean about this context look at the definition of the callback from the consuming view model:

```
  onkeypress(event, keypress, item) {
    switch (keypress) {
      case "tab":
        ...
        return true;
      case "shift+tab":
       ...
       return true;
      case "alt+h":
        ...
        return false;
      case "alt+del":
        ...
        return false;
      }
  }
```

If you compare it's signature with the default callback in the custom element you should notice that `event` and `keypress` are provided by the custom element but where does `item` come from! That's actually defined by the consuming view! I'll list the consuming view template again but this time give a little more context:

```
  <div repeat.for="item of items">
    <keyboard 
        keycombo.bind="keys" 
        onkeypress.call="onkeypress(event, keypress, item)">
      <input type="text" value.bind="item.text1">
      <input type="text" value.bind="item.text2">
    </keyboard>
  </div>
```

What this now shows is that I've placed the `keyboard` custom element inside a `repeat.for`. The `.call` binding can now define the `event` and `keypress` properties (which are mapped in the custom element) but Aurelia allows us to pass our own properties which will automatically be forwarded on! This is great since now I can pass in `item` which is the current item from the items collection. My `onkeypress` callback in the consuming view model can us information from that `item` parameter to make decisions on how that keypress should be actioned. For example, in my case `alt+del` deletes the current row in the `repeat` which has focus.

I like using the custom element so far because of the following advantages:

* The element defines a convenient scope within which the keypresses operate.
* The custom element handles cleaning up the Mousetrap instance.
* `this` correctly points at the view model when the callback fires so data and methods are easily accessible.
* The `.call` syntax allowing custom parameters to be passed makes working out context simple.
* The keypress will bubble up the tree to any other containing keyboard elements depending on the return value from the callback (`return false` cancels the bubble.)
* I've not written an unit tests for this but given Mousetrap is no longer referenced I imagine the consuming view models should be easy to test.

To be honest, the only thing I don't like is the switch statement in the callback used to detect which key press occurred - but for the moment I can live with that given the advantages already listed.
