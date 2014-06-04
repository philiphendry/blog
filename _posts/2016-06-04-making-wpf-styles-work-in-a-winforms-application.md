---
author: Philip Hendry
title: Making WPF Styles work in a WinForms Application
date: 2014-06-04 12:47:14+00:00
comments: true
layout: post
tags:
- WPF
---

### History... skip if you want the detail!

One of the .NET projects I work on is almost 10 years old now and it's starting to show! In particular the WinForms client application  is starting to creak under the strain of having so many developers work on it over the years leaving the code behind the forms quite a mess.

For this reason we've made steps to introduce an MVVM pattern into the old code which separates and pushes a lot of complexity back into the ViewModel and the Model. However, this still left WinForms requiring quite a bit of code-behind to manage the binding between the form and the ViewModel. But this was all for a reason... with any luck we could remove the WinForm and Code-Behind and replace with a single XAML file declaratively bound to the ViewModel.

### Styles did not apply

And that's when we met one of a number of problems. We've already had to tackle [adding resources to a WinForm host](http://drwpf.com/blog/2007/10/05/managing-application-resources-when-wpf-is-hosted/) but we were left wondering why the styles wouldn't apply in the designer.

The solution was to create a new WPF application assembly which meant the App.xaml was being interpreted by the designer although we still added a static method to initialise the resources when the WinForms app started:

```
public partial class App
{
  public static void Initialise()
  {
     if (Current == null)
     {
        new Application();
        var resourceLocator = new Uri("WpfAssembly;component/resources/ControlStyles.xaml",
                UriKind.Relative);
        Current.Resources.MergedDictionaries.Add(
                LoadComponent(resourceLocator) as ResourceDictionary);
     }
  }
}
```