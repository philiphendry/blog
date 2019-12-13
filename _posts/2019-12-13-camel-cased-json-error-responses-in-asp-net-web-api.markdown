---
author: Philip Hendry
title: Camel Cased JSON Error Responses in ASP.NET WebAPI
date: 2019-12-07 19:30
comments: true
layout: post
tags:
- asp-net
---

In my previous post [ASP.NET Web Api Complex Model Binding and Validation]({% post_url 2019-12-07-asp-net-web-api-complex-model-binding-and-valiation %}) I had described how to use ```Request.CreateErrorResponse()``` in either a controller action or a filter to convert the errors held in ```ModelState``` into JSON that can be passed back to the client.

What I hadn't taken into account was the camel casing in the result that the framework had failed to fix. For example, given a class structure like this:

```
public class Person
{
    public Address Address { get; set; }
}
public class Address
{
    [Required] public String AddressLine1 { get; set; }
    [Required] public String Town { get; set; }
    [Required] public String PostCode { get; set; }
}
```

And if I don't submit those fields to the _PUT_ method then I'll get back the following errors:

```
{
    "Message": "The request is invalid.",
    "ModelState": {
        "person.Address.AddressLine1": [
            "The AddressLine1 field is required."
        ],
        "person.Address.Town": [
            "The Town field is required."
        ],
        "person.Address.PostCode": [
            "The PostCode field is required."
        ]
    }
}
```

That problem here is that the naming strategy registered with NewtonSoft has kicked in after the ```ModelState``` has concatenated the field names and therefore only the first letter has been lowercased. In other words, where I see ```person.Address.PostCode``` I actually should see ```person.address.postCode```.

Like any good developer, before reading the docs, I took to google and within a couple of minutes found a (stackoverflow)[https://stackoverflow.com/questions/54308041/how-to-use-json-net-camelcasepropertynamescontractresolver-with-dot-separated-na] post which had the answer!

The first place to change was in the ```WebApiConfig.Register```: 

```
var contractResolver = HtmlSanitisingContractResolver.Instance;
contractResolver.NamingStrategy = new PiecewiseNamingStrategy(new CamelCaseNamingStrategy())
{
    OverrideSpecifiedNames = true,
    ProcessDictionaryKeys = true
};
```

As an added bonus the stackoverflow post also answered the question of how to resolve my ```HtmlSanitisingContractResolver``` from being derived from ```CamelCaseNamingStrategy```. The ```NamingStrategy``` is assigned to the ```HtmlSanitisingContractResolver``` and has a new one called ```PiecewiseNamingStrategy``` which simply splits the name on ```.``` and camelcases each individual item before re-joining them:

```
public class PiecewiseNamingStrategy : NamingStrategy
{
    readonly NamingStrategy baseStrategy;

    public PiecewiseNamingStrategy(NamingStrategy baseStrategy)
    {
        this.baseStrategy = baseStrategy ?? throw new ArgumentNullException();
    }

    protected override string ResolvePropertyName(string name)
    {
        return String.Join(".", name.Split('.')
            .Select(n => baseStrategy.GetPropertyName(n, false)));
    }
}
```

Simple but effective. My next post however will discuss throwing this away in favour of reformatting the error response JSON entirely!