---
author: Philip Hendry
title: Updating a WPF Control Whenever the DataContext Changes
date: 2014-06-04 12:33:00+00:00
comments: true
layout: post
tags:
- WPF
---

I've been writing customised versions of the built-in WPF controls for a specific project I'm working on that isolate specific requirements for the controls in one place rather than having that declared in the XAML. However, one of the obstacles to overcome was receiving a notification whenever the DataContext changes and, more importantly, is initial set, so I can set up the controls bindings. In the full example included below this includes fetching the list of ComboBox items from an enumeration which is the data type of the model item being bound to.

The key to the solution is the creation of a dependency property which is private and has no public getters/setters. Any dependency property can then be registered with a PropertyMetadata callback:

```
private static readonly DependencyProperty PropertyTypeProperty =    
    DependencyProperty.Register("PropertyType", typeof (Object), typeof (ComboBox), 
        new PropertyMetadata(DataContextCallback)
    );
```

The full solution includes a number of other activities but which are mostly driven off this callback:

```
public class ComboBox : System.Windows.Controls.ComboBox
{
    // This dependency property is only required to cause a DataContextCallback 
    // to fire in which we can configure the combo drop down items
    private static readonly DependencyProperty PropertyTypeProperty =    
        DependencyProperty.Register("PropertyType", typeof (Object), typeof (ComboBox), 
            new PropertyMetadata(DataContextCallback)
        );

    public ComboBox()
    {            
        DisplayMemberPath = "DisplayName";
        SelectedValuePath = "Value";
        SetBinding(PropertyTypeProperty, new Binding());
    }

    private static void DataContextCallback(DependencyObject sender, 
        DependencyPropertyChangedEventArgs args)
    {
        var control = sender as ComboBox;
        var boundPropertyName = ControlBehaviour.GetBoundPropertyName(control, SelectedValueProperty);
        ControlBehaviour.AssignCustomBinding(control, SelectedValueProperty, boundPropertyName);
        ControlBehaviour.AssignVisibilityBinding(control, boundPropertyName);
        ControlBehaviour.AssignEnumDescriptionsToItemSource(control, boundPropertyName);
    }
}
```
