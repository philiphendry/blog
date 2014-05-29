---
author: philiphendry
comments: true
date: 2009-07-07 20:12:04+00:00
layout: post
slug: microsoft-windows-presentation-foundation
title: Microsoft Windows Presentation Foundation
wordpress_id: 233
categories:
- .Net
- WPF
---

I'm currently reading [Windows Presentation Foundation Unleashed](http://www.amazon.co.uk/Windows-Presentation-Foundation-Unleashed-WPF/dp/0672328917/ref=sr_1_1?ie=UTF8&s=books&qid=1209978265&sr=8-1) and so far I'm finding it an excellent book and would highly recommend it to anyway who wants to dive head first into WPF. This page represents my notes taken as I've read through the book but it's not substitute for the book!

 

## XAML (eXtensible Application Markup Language)

 

XAML is a technology used to construct and initialise .Net objects and can be used independently of WPF and in conjunction with other technologies (such as Windows Workflow.)

 

The following quick examples defines a button that fills the entire viewing area and, with the .xaml extension, can be viewed in IE.

 
```    
<Button xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" Content="Press Me!">
  <Button.Background>
    LightGreen
  </Button.Background>
</Button>
```

The element name 'Button' represents the name of a class and is known as an _object element_. The XAML compiler must be told where to find these objects and this is done by specifying namespaces. The _Content_ attribute is called a _property attribute _and is a property on the _Button_ object and initialised to a string, although it will be seen later that the Content property is of type _Object_ and can accept far more than just a string. 

The class must provide a parameterless constructor since all the properties are initialised after construction. The _Button.Background_ element is called a _property element_ and is an alternative way to provide values (often complex property values such as xml) for properties on the _Button_ object. The XAML above is equivalent to :
    
```
Button b = new Button();
b.Content = "Press me!";
b.Background = new SolidBrush(Color.LightBlue);
```

### Namespaces

In order for XAML to know where a class can be found to instantiate, the Xml namespaces for these objects must be defined in advance of there use. The _[http://schemas.microsoft.com/winfx/2006/xaml/presentation](http://schemas.microsoft.com/winfx/2006/xaml/presentation)_ mapping to .Net namespaces is hard-coded in the WPF assemblies using _XmlnsDefinitionAttribute_. This namespace defines a number of namespaces including :

 
  * System.Windows 
 
  * System.Windows.Controls 
 
  * System.Windows.Data 
 
  * System.Windows.Media 
 
  * System.Windows.Document 





Another namespace, [http://schemas.microsoft.com/winfx/2006/xaml](http://schemas.microsoft.com/winfx/2006/xaml), maps to types in the .Net namespace _System.Windows.Markup_ but also includes some special directives for the XAML compiler.





### Type Converters





Every time a property is set in XAML, whether as an attribute or property element, the compiler must determine how to translate the string data into something that can be assigned to the underlying .Net object.





The XAML compiler uses the context of the current property to look up a suitable converter where each converter implements _TypeConverter._





The _Background_ property above would be equivalently written in C# as :




    
```
b.Background = (Brush)System.ComponentModel.TypeDescriptor.GetConverter(
    typeof(Brush)).ConvertFromInvariantString("LightBlue");
```




### Markup Extensions





Markup extensions are the preferred approach to extending functionality in XAML (type converters use a fairly 'hidden' syntax.) The markup extensions string is interpreted and returns an object. Anything appearing inside curly braces ({}) will be interpreted as a markup extension. The curly braces can be escaped by prefixing with empty braces such as {}{Binding Path=Height}.





A markup extension can be provided with a number of parameters which can either be positional and passed to a constructor or named which will be assigned to the correspondingly named properties on the markup object. For example:




    
```
<Button Background="{x:Null}"
            Height="{x:Static SystemParameters.IconHeight}"
            Content="{Binding Path=Height RelativeSource={RelativeSource Self}}" />
```




In this example SystemParameters.IconHeight and Self are _positional parameters_ and Path and RelativeSource are _named parameters_. This example also demonstrates how markup extensions can be nested.





Because the markup extensions implement a default constructor they can also use property element syntax (so long as positional parameters are also represented on the object as properties as well as parameters to a constructor) :



```
<Button>
    <Button.Background>
        <x:Null/>
    </Button.Background>
</Button>
```
    



