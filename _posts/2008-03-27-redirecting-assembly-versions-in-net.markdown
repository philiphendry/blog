---
author: philiphendry
comments: true
date: 2008-03-27 11:29:54+00:00
layout: post
slug: redirecting-assembly-versions-in-net
title: Redirecting Assembly Versions in .Net
wordpress_id: 63
categories:
- .Net
- Dev Problem
- Microsoft
- Visual Studio
---

We were having a problem with deserialization on one of our projects and although the cause seemed obvious the solution wasn't!

The error we were getting is at the bottom of this post. It says that one type cannot be converted to another type but both types are the same! However, what it isn't highlighting is that the version number of the assembly that these two types come from is different. When an object is serialized the assembly versions of the objects being serialized are being stored too and when the deserializing these versions are compared to what can be created and if they don't match the error is thrown.

One solution (I thought) would be to changed the _AssemblyFormat_ property on the binary formatter as shown below. However, this simply does not work. There's been a bug raised with Microsoft for this issue a year ago but it doesn't look like it's been fixed!
    
    <span style="color:#2b91af;">BinaryFormatter </span>formatter = <span style="color:blue;">new </span><span style="color:#2b91af;">BinaryFormatter</span>();
    formatter.AssemblyFormat = <span style="color:#2b91af;">FormatterAssemblyStyle</span>.Simple;




So the next option that was suggest by several other blogs was to implement a _SerializationBinder_ that can intercept the requests for the assemblies to create and strip off the assembly version. This, however, is just plain awful since we're now writing even more code to a simple problem. Here's some template code:
    
    <span style="color:blue;">class </span><span style="color:#2b91af;">Program
        </span>{
            <span style="color:blue;">static void </span>Main(<span style="color:blue;">string</span>[] args)
            {
                <span style="color:#2b91af;">BinaryFormatter </span>formatter = <span style="color:blue;">new </span><span style="color:#2b91af;">BinaryFormatter</span>();
                formatter.Binder = <span style="color:blue;">new </span><span style="color:#2b91af;">SimpleBinder</span>();
            }
        }
    
        <span style="color:blue;">class </span><span style="color:#2b91af;">SimpleBinder </span>: <span style="color:#2b91af;">SerializationBinder
        </span>{
            <span style="color:blue;">public override </span><span style="color:#2b91af;">Type </span>BindToType(<span style="color:blue;">string </span>assemblyName, <span style="color:blue;">string </span>typeName)
            {
                <span style="color:green;">/* Implement code to modify the typeName */
                </span><span style="color:blue;">return </span><span style="color:#2b91af;">Type</span>.GetType(typeName);
            }
        }

[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)


The third option, and certainly the simplest, is to modify the [assembly bindings to redirect](http://msdn2.microsoft.com/en-us/library/7wd6ex19.aspx) one version of an assembly to another. In the example below the _bindingRedirect_ allows requests for version 1.0.0.0 of this particular assembly to be serviced by version 2.0.0.0. So long as we're sure the deserializing functionality is compatible, this solves the problem without having to change any code.
    
    <span style="color:blue;"><</span><span style="color:#a31515;">assemblyBinding </span><span style="color:red;">xmlns</span><span style="color:blue;">=</span>"<span style="color:blue;">urn:schemas-microsoft-com:asm.v1</span>"<span style="color:blue;">>
        <</span><span style="color:#a31515;">dependentAssembly</span><span style="color:blue;">>
            <</span><span style="color:#a31515;">assemblyIdentity </span><span style="color:red;">name</span><span style="color:blue;">=</span>"<span style="color:blue;">Configuration</span>" <span style="color:red;">publicKeyToken</span><span style="color:blue;">=</span>"<span style="color:blue;">.........</span>" <span style="color:red;">culture</span><span style="color:blue;">=</span>""<span style="color:blue;">/>
            <</span><span style="color:#a31515;">bindingRedirect </span><span style="color:red;">oldVersion</span><span style="color:blue;">=</span>"<span style="color:blue;">1.0.0.0</span>" <span style="color:red;">newVersion</span><span style="color:blue;">=</span>"<span style="color:blue;">2.0.0.0</span>" <span style="color:blue;">/>
            <</span><span style="color:#a31515;">codeBase </span><span style="color:red;">href</span><span style="color:blue;">=</span>"<span style="color:blue;">C:\bin\Configuration.dll</span>" <span style="color:red;">version</span><span style="color:blue;">=</span>"<span style="color:blue;">2.0.0.0</span>" <span style="color:blue;">/>
        </</span><span style="color:#a31515;">dependentAssembly</span><span style="color:blue;">>
    </</span><span style="color:#a31515;">assemblyBinding</span><span style="color:blue;">></span>

[](http://11011.net/software/vspaste)


Here's the error :




Object of type 'com.iMeta.metaCore.Utilities.Base.Pair`2[System.Type,System.Collections.Generic.List`1[M.Box.Core.Command]][]' cannot be converted to type 'com.iMeta.metaCore.Utilities.Base.Pair`2[System.Type,System.Collections.Generic.List`1[M.Box.Core.Command]][]'. (Exception Type: ArgumentException, Stack: at System.RuntimeType.CheckValue(Object value, Binder binder, CultureInfo culture, BindingFlags invokeAttr)  
at System.Reflection.RtFieldInfo.InternalSetValue(Object obj, Object value, BindingFlags invokeAttr, Binder binder, CultureInfo culture, Boolean doVisibilityCheck, Boolean doCheckConsistency)  
at System.Runtime.Serialization.SerializationFieldInfo.InternalSetValue(Object obj, Object value, BindingFlags invokeAttr, Binder binder, CultureInfo culture, Boolean requiresAccessCheck, Boolean isBinderDefault)  
at System.Runtime.Serialization.FormatterServices.SerializationSetValue(MemberInfo fi, Object target, Object value)  
at System.Runtime.Serialization.ObjectManager.CompleteObject(ObjectHolder holder, Boolean bObjectFullyComplete)  
at System.Runtime.Serialization.ObjectManager.DoNewlyRegisteredObjectFixups(ObjectHolder holder)  
at System.Runtime.Serialization.ObjectManager.RegisterObject(Object obj, Int64 objectID, SerializationInfo info, Int64 idOfContainingObj, MemberInfo member, Int32[] arrayIndex)  
at System.Runtime.Serialization.Formatters.Binary.ObjectReader.RegisterObject(Object obj, ParseRecord pr, ParseRecord objectPr, Boolean bIsString)  
at System.Runtime.Serialization.Formatters.Binary.ObjectReader.ParseObjectEnd(ParseRecord pr)  
at System.Runtime.Serialization.Formatters.Binary.ObjectReader.Parse(ParseRecord pr)  
at System.Runtime.Serialization.Formatters.Binary.__BinaryParser.Run()  
at System.Runtime.Serialization.Formatters.Binary.ObjectReader.Deserialize(HeaderHandler handler, __BinaryParser serParser, Boolean fCheck, Boolean isCrossAppDomain, IMethodCallMessage methodCallMessage)  
at System.Runtime.Serialization.Formatters.Binary.BinaryFormatter.Deserialize(Stream serializationStream, HeaderHandler handler, Boolean fCheck, Boolean isCrossAppDomain, IMethodCallMessage methodCallMessage)  
at System.Runtime.Serialization.Formatters.Binary.BinaryFormatter.Deserialize(Stream serializationStream)
