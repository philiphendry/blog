---
author: philiphendry
comments: true
date: 2008-05-25 20:40:24+00:00
layout: post
slug: software-dependencies
title: Software Dependencies
wordpress_id: 70
tags:
- dev-tools
- design
---

## Tools

  * [NDepend](http://ndepend.com/) can be used to analyse the dependencies in a project. And there's a [review](http://searchwindevelopment.techtarget.com/tip/0,289483,sid8_gci1274598,00.html) too.  
  * [Castle Windsor](http://www.castleproject.org/container/index.html) is a fully-fledged open-source Inversion of Control tool.  
  * [Binsor](http://www.ayende.com/Blog/archive/2006/09/16/7268.aspx) (a DSL built for Castle Windsor) and [Boo](http://boo.codehaus.org/) makes writing Castle Windsor scripts a lot easier.  
  * [Structure Map](http://structuremap.sourceforge.net/Default.htm) is another dependency injection framework.  
  * Wikipedia has links to a number of [Mock Object](http://en.wikipedia.org/wiki/Mock_object) frameworks.

## Definition

Code must have dependencies - you wouldn't get much done without referencing and depending on system libraries in the .Net Framework for example. However, whenever a component is designed the question of _reversibility_ should be considered - how easy would it be to back-track on a particular design decision and replace it with another Maybe a better question is _how can this be tested in isolation_? In particular, testing in isolation speeds tests up and prevents an error in one component being raised back to another's test case.

## Dependency Inversion

There are a number of different ways to implement this and in particular [Martin Fowlers](http://www.martinfowler.com/articles/injection.html) article on this or [Robert C. Martin's](http://objectmentor.com/resources/articles/dip.pdf). However, the basic premise is to implement to a contract - the contract being an interface definition. The concrete implementation of the interface can be created in a variety of ways and given to the component. For example, the following code uses a _Service Locator:_
    
```
class ServiceLocator
{
    public static T Find<T>()
    {
        return default(T);
    }
}

class InvoiceService
{
    private IAuthorizationService authorizationService;

    public InvoiceService()
    {
        this.authorizationService = ServiceLocator.Find<IAuthorizationService>();
    }
}
```

The Service Locator can decide itself where and how to generate the concrete implementations.




## Dependency Injection




Instead of (or as well as) using a Service Locator you could create a parameterised constructor (or other mechanism) to pass the concrete dependencies into a component. This is especially useful when isolating a component for testing and passing it [Mock Objects](http://msdn.microsoft.com/msdnmag/issues/07/09/MockTesting) that can be used to confirm the correct interaction between components.




## Inversion of Control Containers




IoC containers provide a central location to manage dependencies - in it's simplest guise just a service locator with a different name. Here is an example of a simple IoC container or _Dependency Resolver_:
    
```
interface IDependencyResolver
{
    T Resolve<T>();
}

class SimpleDependencyResolver : IDependencyResolver
{
    public readonly Dictionary<Type, object> m_Types =
        new Dictionary<Type, object>();

    public T Resolve<T>()
    {
        return (T)m_Types[typeof(T)];
    }

    // Register is not defined on the interface because only
    // the creator need call it
    public void Register<T>(object obj)
    {
        if (obj is T == false)
        {
            throw new InvalidOperationException();
        }
        m_Types.Add(typeof(T), obj);
    }
}
```



## Static Gateway




Rather than pass the Dependency Resolver to every component it's easier to define a Static Gateway:
    
```
class DependencyResolver
{
    private static IDependencyResolver s_Inner;

    public static void Initialize(IDependencyResolver resolver)
    {
        s_Inner = resolver;
    }

    public static T Resolve<T>()
    {
        return s_Inner.Resolve<T>();
    }
}
```



This leads to the option that each class's default constructor can now create the concrete classes it needs without knowing where they came from:
    
```
class InvoiceService
{
    private IAuthorizationService authorizationService;

    public InvoiceService()
    {
        this.authorizationService = DependencyResolver.Resolve<IAuthorizationService>();
    }
}

class Program
{
    static void Main(string[] args)
    {
        SimpleDependencyResolver s = new SimpleDependencyResolver();
        s.Register<IAuthorizationService>(new AuthorizationService());

        DependencyResolver.Initialize(s);
        InvoiceService invoiceService = new InvoiceService();
    }

}
```
