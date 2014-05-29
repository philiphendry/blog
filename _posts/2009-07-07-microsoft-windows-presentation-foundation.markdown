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

 
    
    <span style="color:#8b008b;"><Button </span><span style="color:red;">xmlns</span><span style="color:blue;">="http://schemas.microsoft.com/winfx/2006/xaml/presentation" </span><span style="color:red;">Content</span><span style="color:blue;">="Press Me!"</span><span style="color:#8b008b;">>
      <Button.Background>
        </span><span style="color:black;">LightGreen
      </span><span style="color:#8b008b;"></Button.Background>
    </Button></span>


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)



The element name 'Button' represents the name of a class and is known as an _object element_. The XAML compiler must be told where to find these objects and this is done by specifying namespaces. The _Content_ attribute is called a _property attribute _and is a property on the _Button_ object and initialised to a string, although it will be seen later that the Content property is of type _Object_ and can accept far more than just a string. 





The class must provide a parameterless constructor since all the properties are initialised after construction. The _Button.Background_ element is called a _property element_ and is an alternative way to provide values (often complex property values such as xml) for properties on the _Button_ object. The XAML above is equivalent to :




    
    <span style="color:#2b91af;">Button </span>b = <span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>();
    b.Content = <span style="color:#a31515;">"Press me!"</span>;
    b.Background = <span style="color:blue;">new </span><span style="color:#2b91af;">SolidBrush</span>(<span style="color:#2b91af;">Color</span>.LightBlue);


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)



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




    
    b.Background = (<span style="color:#2b91af;">Brush</span>)System.ComponentModel.<span style="color:#2b91af;">TypeDescriptor</span>.GetConverter(
        <span style="color:blue;">typeof</span>(<span style="color:#2b91af;">Brush</span>)).ConvertFromInvariantString(<span style="color:#a31515;">"LightBlue"</span>);





### [](http://11011.net/software/vspaste)Markup Extensions





Markup extensions are the preferred approach to extending functionality in XAML (type converters use a fairly 'hidden' syntax.) The markup extensions string is interpreted and returns an object. Anything appearing inside curly braces ({}) will be interpreted as a markup extension. The curly braces can be escaped by prefixing with empty braces such as {}{Binding Path=Height}.





A markup extension can be provided with a number of parameters which can either be positional and passed to a constructor or named which will be assigned to the correspondingly named properties on the markup object. For example:




    
    <span style="color:blue;"><</span><span style="color:#a31515;">Button </span><span style="color:red;">Background</span><span style="color:blue;">="{</span><span style="color:#a31515;">x</span><span style="color:blue;">:</span><span style="color:#a31515;">Null</span><span style="color:blue;">}"
                </span><span style="color:red;">Height</span><span style="color:blue;">="{</span><span style="color:#a31515;">x</span><span style="color:blue;">:</span><span style="color:#a31515;">Static </span><span style="color:red;">SystemParameters</span><span style="color:blue;">.</span><span style="color:red;">IconHeight</span><span style="color:blue;">}"
                </span><span style="color:red;">Content</span><span style="color:blue;">="{</span><span style="color:#a31515;">Binding </span><span style="color:red;">Path</span><span style="color:blue;">=Height </span><span style="color:red;">RelativeSource</span><span style="color:blue;">={</span><span style="color:#a31515;">RelativeSource </span><span style="color:red;">Self</span><span style="color:blue;">}}" /></span>





[](http://11011.net/software/vspaste)In this example SystemParameters.IconHeight and Self are _positional parameters_ and Path and RelativeSource are _named parameters_. This example also demonstrates how markup extensions can be nested.





Because the markup extensions implement a default constructor they can also use property element syntax (so long as positional parameters are also represented on the object as properties as well as parameters to a constructor) :




    
    <span style="color:blue;"><</span><span style="color:#a31515;">Button</span><span style="color:blue;">>
        <</span><span style="color:#a31515;">Button.Background</span><span style="color:blue;">>
            <</span><span style="color:#a31515;">x</span><span style="color:blue;">:</span><span style="color:#a31515;">Null</span><span style="color:blue;">/>
        </</span><span style="color:#a31515;">Button.Background</span><span style="color:blue;">>
    </</span><span style="color:#a31515;">Button</span><span style="color:blue;">></span>


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)
