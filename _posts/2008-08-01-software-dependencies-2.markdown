---
author: philiphendry
comments: true
date: 2008-08-01 07:32:12+00:00
layout: post
slug: software-dependencies-2
title: Software Dependencies
wordpress_id: 158
categories:
- Design
---

## Tools

 

  
  * [NDepend](http://ndepend.com/) can be used to analyse the dependencies in a project. And there's a [review](http://searchwindevelopment.techtarget.com/tip/0,289483,sid8_gci1274598,00.html) too. 
   
  * [Castle Windsor](http://www.castleproject.org/container/index.html) is a fully-fledged open-source Inversion of Control tool. 
   
  * [Binsor](http://www.ayende.com/Blog/archive/2006/09/16/7268.aspx) (a DSL built for Castle Windsor) and [Boo](http://boo.codehaus.org/) makes writing Castle Windsor scripts a lot easier. 
   
  * [Structure Map](http://structuremap.sourceforge.net/Default.htm) is another dependency injection framework. 
   
  * Wikipedia has links to a number of [Mock Object](http://en.wikipedia.org/wiki/Mock_object) frameworks.
   
  * [autofac](http://code.google.com/p/autofac/) is available on Google Code
 

## Definition

 

Code must have dependencies - you wouldn't get much done without referencing and depending on system libraries in the .Net Framework for example. However, whenever a component is designed the question of _reversibility_ should be considered - how easy would it be to back-track on a particular design decision and replace it with another Maybe a better question is _how can this be tested in isolation_? In particular, testing in isolation speeds tests up and prevents an error in one component being raised back to another's test case.

 

## Dependency Inversion

 

There are a number of different ways to implement this and in particular [Martin Fowlers](http://www.martinfowler.com/articles/injection.html) article on this or [Robert C. Martin's](http://objectmentor.com/resources/articles/dip.pdf). However, the basic premise is to implement to a contract - the contract being an interface definition. The concrete implementation of the interface can be created in a variety of ways and given to the component. For example, the following code uses a _Service Locator:_

 
    
    <span style="color:blue;">class </span><span style="color:#2b91af;">ServiceLocator
    </span>{
        <span style="color:blue;">public static </span>T Find<T>()
        {
            <span style="color:blue;">return default</span>(T);
        }
    }
    
    <span style="color:blue;">class </span><span style="color:#2b91af;">InvoiceService
    </span>{
        <span style="color:blue;">private </span><span style="color:#2b91af;">IAuthorizationService </span>authorizationService;
    
        <span style="color:blue;">public </span>InvoiceService()
        {
            <span style="color:blue;">this</span>.authorizationService = <span style="color:#2b91af;">ServiceLocator</span>.Find<<span style="color:#2b91af;">IAuthorizationService</span>>();
        }
    }


[](http://11011.net/software/vspaste)



The Service Locator can decide itself where and how to generate the concrete implementations.





## Dependency Injection





Instead of (or as well as) using a Service Locator you could create a parameterised constructor (or other mechanism) to pass the concrete dependencies into a component. This is especially useful when isolating a component for testing and passing it [Mock Objects](http://msdn.microsoft.com/msdnmag/issues/07/09/MockTesting) that can be used to confirm the correct interaction between components.





## Inversion of Control Containers





IoC containers provide a central location to manage dependencies - in it's simplest guise just a service locator with a different name. Here is an example of a simple IoC container or _Dependency Resolver_:




    
    <span style="color:blue;">interface </span><span style="color:#2b91af;">IDependencyResolver
    </span>{
        T Resolve<T>();
    }
    
    <span style="color:blue;">class </span><span style="color:#2b91af;">SimpleDependencyResolver </span>: <span style="color:#2b91af;">IDependencyResolver
    </span>{
        <span style="color:blue;">public readonly </span><span style="color:#2b91af;">Dictionary</span><<span style="color:#2b91af;">Type</span>, <span style="color:blue;">object</span>> m_Types =
            <span style="color:blue;">new </span><span style="color:#2b91af;">Dictionary</span><<span style="color:#2b91af;">Type</span>, <span style="color:blue;">object</span>>();
    
        <span style="color:blue;">public </span>T Resolve<T>()
        {
            <span style="color:blue;">return </span>(T)m_Types[<span style="color:blue;">typeof</span>(T)];
        }
    
        <span style="color:green;">// Register is not defined on the interface because only
        // the creator need call it
        </span><span style="color:blue;">public void </span>Register<T>(<span style="color:blue;">object </span>obj)
        {
            <span style="color:blue;">if </span>(obj <span style="color:blue;">is </span>T == <span style="color:blue;">false</span>)
            {
                <span style="color:blue;">throw new </span><span style="color:#2b91af;">InvalidOperationException</span>();
            }
            m_Types.Add(<span style="color:blue;">typeof</span>(T), obj);
        }
    }





## [](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)Static Gateway





Rather than pass the Dependency Resolver to every component it's easier to define a Static Gateway:




    
    <span style="color:blue;">class </span><span style="color:#2b91af;">DependencyResolver
    </span>{
        <span style="color:blue;">private static </span><span style="color:#2b91af;">IDependencyResolver </span>s_Inner;
    
        <span style="color:blue;">public static void </span>Initialize(<span style="color:#2b91af;">IDependencyResolver </span>resolver)
        {
            s_Inner = resolver;
        }
    
        <span style="color:blue;">public static </span>T Resolve<T>()
        {
            <span style="color:blue;">return </span>s_Inner.Resolve<T>();
        }
    }





This leads to the option that each class's default constructor can now create the concrete classes it needs without knowing where they came from:




    
    <span style="color:blue;">class </span><span style="color:#2b91af;">InvoiceService
    </span>{
        <span style="color:blue;">private </span><span style="color:#2b91af;">IAuthorizationService </span>authorizationService;
    
        <span style="color:blue;">public </span>InvoiceService()
        {
            <span style="color:blue;">this</span>.authorizationService = <span style="color:#2b91af;">DependencyResolver</span>.Resolve<<span style="color:#2b91af;">IAuthorizationService</span>>();
        }
    }


[](http://11011.net/software/vspaste)


    
    <span style="color:blue;">class </span><span style="color:#2b91af;">Program
    </span>{
        <span style="color:blue;">static void </span>Main(<span style="color:blue;">string</span>[] args)
        {
            <span style="color:#2b91af;">SimpleDependencyResolver </span>s = <span style="color:blue;">new </span><span style="color:#2b91af;">SimpleDependencyResolver</span>();
            s.Register<<span style="color:#2b91af;">IAuthorizationService</span>>(<span style="color:blue;">new </span><span style="color:#2b91af;">AuthorizationService</span>());
    
            <span style="color:#2b91af;">DependencyResolver</span>.Initialize(s);
            <span style="color:#2b91af;">InvoiceService </span>invoiceService = <span style="color:blue;">new </span><span style="color:#2b91af;">InvoiceService</span>();
        }
    
    }


[](http://11011.net/software/vspaste)
