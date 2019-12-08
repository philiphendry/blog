---
author: Philip Hendry
title: ASP.NET Web Api Complex Model Binding and Validation
date: 2019-12-07 19:30
comments: true
layout: post
tags:
- asp-net
---
I've been writing an application that combines ASP.NET MVC/WebAPI backend and a Vue.JS single page application frontend over the last few years. It started as a bespoke ASP.NET MVC application but I've slowly been growing the code base into a multi-tenant product with quite a complex form editor written in Vue.JS. It's this form editor that I'm focusing on in this post and in particular how it transfers quite a complex Javacript object model to/from an ASP.NET WebAPI interface. The rest of this post focuses on how JSON is mapped to an object property on a WebAPI action and the extra validation I want to have.

The Javascript model I transfer is quite complex so for this article I'm going to choose a highly contrived example that demonstrates all the key features I need implementing and in particular:

* Mapping of simple types such as string, int and datetime.
* Mapping of arrays.
* Validation such as length, range and required.
* Sanitisation of string fields marked as HTML.
* Automatic validation of the model before the action is called.

# Setup

For my experiments I'm using Visual Studio 2019 and started with an _ASP.NET Web Application (.NET Framework)_, _.NET Framework 4.7.2_ and chose the _Empty_ template - sorry, I'm not using ASP.NET Core just yet!

Install the NuGet package _Microsoft.AspNet.WebAPI_ and create folders _App__Code_, _Controllers_ and _Models_.

We'll start with the simplest of configurations so create a class file _App__Code/WebApiConfig.cs_ and add the following to configure route by convention:

```
using System.Web.Http;

namespace JsonMapping
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

And to have this configuration called create a _Global.asax_ and ass:

```
using System.Web.Http;

namespace JsonMapping
{
    public class Global : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            GlobalConfiguration.Configure(WebApiConfig.Register);
        }
    }
}
```

We're going to need a model that the controller we accept and return so in the _Models_ folder create:

```
namespace JsonMapping.Models
{
    public class Person
    {
        public string Name { get; set; }
    }
}
```

And finally a controller to call so in the _Controllers_ folder create _PersonController_ and for now add the following:

```
using System.Web.Http;
using JsonMapping.Models;

namespace JsonMapping.Controllers
{
    public class PersonController : ApiController
    {
        public Person Get()
        {
            return new Person
            {
                Name = "Bob"
            };
        }

        public Person Post(Person person)
        {
            return person;
        }
    }
}
```

With the initially coding done it's almost time to start up and test but before that open the project properties and in the Web tab change the _Start Action_ to _Don't open a page._ as I'm testing the API with [https://www.getpostman.com/downloads/](PostMan) and don't want an extra browser opening. Whilst in the project properties grab the _Project URL_ since this will be need in PostMan.

# First Test

In PostMan if I create a simple GET request with the url ```http://localhost:9570/api/person``` (you're port will probably be different) then the result will be:

```
{
    "Name": "Bob"
}
```

# Simple Type Validation

The next step is to see how to implement some simple property validations. Using data annotations I can change the person class to this:

```
using System.ComponentModel.DataAnnotations;

namespace JsonMapping.Models
{
    public class Person
    {
        [Required]
        [StringLength(10)]
        public string Name { get; set; }
    }
}
```

Then the post action method can check the ```ModelState``` which is automatically populated with validation information. Even better there's a utility method that will format and return the error information:

```
public HttpResponseMessage Post(Person person)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    return Request.CreateResponse(HttpStatusCode.Created, person);
}
```

And now if I post something like this:

```
{
    "Name": "Bob 'with a really long name' Jones"
}
```

then the response is a detailed message informing us of the error:

```
{
    "Message": "The request is invalid.",
    "ModelState": {
        "person.Name": [
            "The field Name must be a string with a maximum length of 10."
        ]
    }
}
```

## Integer validation

And just to prove other simple value types can also contribute to this validation add the following to `Person`:

```
[Range(0, 150)]
public int Age { get; set; }
```

Then submit this:

```
{
    "Age": 151
}
```

then we get an error detailing the missing _Name_ and the _Age_ that falls outside of the range check:

```
{
    "Message": "The request is invalid.",
    "ModelState": {
        "person.Name": [
            "The Name field is required."
        ],
        "person.Age": [
            "The field Age must be between 0 and 150."
        ]
    }
}
```

# Centralised validation

I'm not keen on having to remember to check the ```ModelState.IsValid``` property in each and every controller action so the proposed solution is to centralise the validation in one filter added like this in ```WebApiConfig.Register```:

```
config.Filters.Add(new ValidateModelAttribute());
```

and the ```ValidateModelAttribute``` is:

```
using System.Net;
using System.Net.Http;
using System.Web.Http.Controllers;
using System.Web.Http.Filters;

namespace JsonMapping.Filters
{
    public class ValidateModelAttribute : ActionFilterAttribute
    {
        public override void OnActionExecuting(HttpActionContext actionContext)
        {
            if (!actionContext.ModelState.IsValid)
            {
                actionContext.Response = actionContext.Request.CreateErrorResponse(
                    HttpStatusCode.BadRequest, actionContext.ModelState);
            }
        }
    }
}
```

This also means the ```PersonController.Post()``` method can go back to looking a lot simpler:

```
public Person Post(Person person)
{
    return person;
}
```

# Enumerations

To see how enumerations appear in the serialised output let's add a ```Colour``` enumeration with a very limited set of colours:

```
public enum Colours
{
    Red,
    Green, 
    Blue
}
```

and a property called ```FavouriteColour``` on the ```Person``` class:

```
public Colours FavouriteColour { get; set; }
```

If I run the _GET_ request I see the following response:

```
{
    "Name": "Bob",
    "Age": 0,
    "FavouriteColour": 0
}
```

I don't like that _0_ for a favourite colour and would much rather see the name of the colour instead. There are a number of different ways to implement this but for my simple implementation I've chosen to use a Newtonsoft out-of-the-box solution called [https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Converters_StringEnumConverter.htm](StringEnumConverter). The [https://www.newtonsoft.com/json/help/html/SerializationSettings.htm#Converters](Serialization Settings documentation) states that this can be used in multiple different ways including adding it as an _Attribute_ to the model property. However, I want as little as possible (hopefully nothing) to be added to the model that is related to the serialisation mechanism. I also want have built-in 'standards' that 'just work' so having them declared globally is my chosen way. To that end I add the following to the ```WebApiConfig.Register``` method to fetch the Newtonsoft serialisation settings and add their provided ```StringEnumConverter```:

```
var serializerSettings = config.Formatters.JsonFormatter.SerializerSettings;
serializerSettings.Converters.Add(new Newtonsoft.Json.Converters.StringEnumConverter());
```

And now the _GET_ request returns named colours:

```
{
    "Name": "Bob",
    "Age": 0,
    "FavouriteColour": "Red"
}
```

And this is two-way so I can post the model above with _Red_ replaced with _Blue_ and the server will reflect find that colour amongst the colours in the enum.

## Unknown enumeration values

What happens if I post a colour that doesn't exist in the enumeration? Here's the output when I _POST_ a _FavouriteColour_ of _Orange_:

```
{
    "Message": "The request is invalid.",
    "ModelState": {
        "person.FavouriteColour": [
            "Error converting value \"Orange\" to type 'JsonMapping.Models.Colours'. Path 'FavouriteColour', line 4, position 32."
        ]
    }
}
```

It validates ok and the message is pretty helpful but type information that the client doesn't need to know has 'leaked' out. It's not a major problem but when I have time in the future I might come back to this and see how I can take control of that message.

# Camel casing property names

Now that the Newtonsoft serialisation settings have been introduced I'll mention the ```CamelCasePropertyNamesContractResolver``` which I like to add since I prefer to see Javascript properties using camel case. Adding this isn't quite the same as the _Converter_ above but it just as simple:

```
serializerSettings.ContractResolver = new CamelCasePropertyNamesContractResolver();
```

and now the response has camel cased property names:

```
{
    "name": "Bob",
    "age": 0,
    "favouriteColour": "Red"
}
```

# Default values

There is a serialisation setting called [https://www.newtonsoft.com/json/help/html/SerializationSettings.htm#DefaultValueHandling](DefaultValueHandling) and if this is set to ```Populate``` then if a value is not provided in the posted JSON then the serialiser will populate the model with the value given by the [https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.defaultvalueattribute?redirectedfrom=MSDN&view=netframework-4.8](DefaultValueAttribute) configured for the property.

However, my personal preference is the model itself sets it's own defaults so that whenever the model is ```new```'d then the defaults will already be set. Therefore I do this in the model which handles setting defaults on deserialisation:

```
private const Colours DefaultFavouriteColour = Colours.Blue;

[DefaultValue(DefaultFavouriteColour)] 
public Colours FavouriteColour { get; set; } = DefaultFavouriteColour;
```

and in ```WebApiConfig.Register``` I tell the serialiser to include default values in the serialised output:

```
serializerSettings.DefaultValueHandling = DefaultValueHandling.Include;
```

# Object mapping

In real life application objects can get quite complex and involve several sub-objects and collections of them. So let's introduce a collection to our `Person` object:

```
public List<Person> Children { get; set; }
```

Not only have I introduce a sub-object, I've put it in a collection as well (I could have just demonstrated adding ```public Person Child { get; set;}``` for example) and this property is self-referencing ```Person```.

And to demonstrate this change the controller ```Get``` method to this:

```
public Person Get()
{
    return new Person
    {
        Name = "Bob",
        Children = new List<Person>
        {
            new Person { Name = "Tim"},
            new Person { Name = "Kate"}
        }
    };
}
```

And to be honest... we're done! There is serialisation property called [https://www.newtonsoft.com/json/help/html/SerializationSettings.htm#ReferenceLoopHandling](ReferenceLoopHandling) which may need to be set when including circular references but in this simple example there's nothing more to be done.

# Polymorhpic object handling

In my real-life application my model represents a complex form on which the user can drag different field types such as text box, radio buttons and paragraphs. The model on the server has a ```FieldBase``` type then derived classes of ```TextBox```, ```RadioButtons``` and ```Paragraph```. However, to deserialise from JSON to the model the deserialiser needs more information in order to figure out which of those sub-types it should create.

For this demonstration I'll pick a slightly more contrived example so let's create a ```FavouriteSport``` on the ```Person``` model of type ```ISport```:

```
public ISport FavouriteSport { get; set; }
```

```ISport``` is an interface with one common property ``Name```:

```
public interface ISport
{
    string Name { get; }
}
```

And then I'll have two sports ```Squash``` and ```Football```. Each of these will contain data that's specific only to that sport but for this I've just added ```CourtName``` and ```PitchName```:

```
public class Squash : ISport
{
    public string Name => "Squash";
    public string CourtName { get; set; }
}

public class Football : ISport
{
    public string Name => "Football";
    public string PitchName { get; set; }
}
```

What this solution needs is a way to teach the Newtonsoft deserialiser how to read the JSON and decide from the JSON which object to create. This requires having a property on the JSON object used to determine the type to create and in my example above this will simply be the ```ISport.Name``` property - admittedly not the best choice since it's not a unique identifier but will demonstrate the method. In my real-life application I have a enumeration of field types and a ```FieldType``` property on ```FieldBase``` to determine which sub-type to include.

> Newtonsoft can handle this polymorphism problem itself with [https://www.newtonsoft.com/json/help/html/SerializationSettings.htm#TypeNameHandling](TypeNameHandling) but this solution writes the full .Net Type information into the JSON which leads to a brittle solution and leaking implementation details to the client.

The following utility class is taken from [https://stackoverflow.com/questions/8030538/how-to-implement-custom-jsonconverter-in-json-net-to-deserialize-a-list-of-base](StackOverflow) and is used as a basis for the specific converters we need to write:

```
using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace JsonMapping.Serialisation
{
    /// <summary>Base Generic JSON Converter that can help quickly define converters for specific types by automatically
    /// generating the CanConvert, ReadJson, and WriteJson methods, requiring the implementer only to define a strongly typed Create method.</summary>
    public abstract class JsonCreationConverter<T> : JsonConverter
    {
        /// <summary>Create an instance of objectType, based properties in the JSON object</summary>
        /// <param name="objectType">type of object expected</param>
        /// <param name="jObject">contents of JSON object that will be deserialized</param>
        protected abstract T Create(Type objectType, JObject jObject);

        /// <summary>Determines if this converted is designed to deserialization to objects of the specified type.</summary>
        /// <param name="objectType">The target type for deserialization.</param>
        /// <returns>True if the type is supported.</returns>
        public override bool CanConvert(Type objectType)
        {
            // FrameWork 4.5
            // return typeof(T).GetTypeInfo().IsAssignableFrom(objectType.GetTypeInfo());
            // Otherwise
            return typeof(T).IsAssignableFrom(objectType);
        }

        public override bool CanWrite { get { return false; } }

        /// <summary>Parses the json to the specified type.</summary>
        /// <param name="reader">Newtonsoft.Json.JsonReader</param>
        /// <param name="objectType">Target type.</param>
        /// <param name="existingValue">Ignored</param>
        /// <param name="serializer">Newtonsoft.Json.JsonSerializer to use.</param>
        /// <returns>Deserialized Object</returns>
        public override object ReadJson(JsonReader reader, Type objectType, object existingValue, JsonSerializer serializer)
        {
            if (reader.TokenType == JsonToken.Null)
                return null;

            // Load JObject from stream
            JObject jObject = JObject.Load(reader);

            // Create target object based on JObject
            T target = Create(objectType, jObject);

            //Create a new reader for this jObject, and set all properties to match the original reader.
            using (JsonReader jObjectReader = CopyReaderForObject(reader, jObject))
            {
                // Populate the object properties
                serializer.Populate(jObjectReader, target);
            }

            return target;
        }

        /// <summary>Serializes to the specified type</summary>
        /// <param name="writer">Newtonsoft.Json.JsonWriter</param>
        /// <param name="value">Object to serialize.</param>
        /// <param name="serializer">Newtonsoft.Json.JsonSerializer to use.</param>
        public override void WriteJson(JsonWriter writer, object value, JsonSerializer serializer)
        {
            serializer.Serialize(writer, value);
        }

        /// <summary>Creates a new reader for the specified jObject by copying the settings
        /// from an existing reader.</summary>
        /// <param name="reader">The reader whose settings should be copied.</param>
        /// <param name="jToken">The jToken to create a new reader for.</param>
        /// <returns>The new disposable reader.</returns>
        private static JsonReader CopyReaderForObject(JsonReader reader, JToken jToken)
        {
            JsonReader jTokenReader = jToken.CreateReader();
            jTokenReader.Culture = reader.Culture;
            jTokenReader.DateFormatString = reader.DateFormatString;
            jTokenReader.DateParseHandling = reader.DateParseHandling;
            jTokenReader.DateTimeZoneHandling = reader.DateTimeZoneHandling;
            jTokenReader.FloatParseHandling = reader.FloatParseHandling;
            jTokenReader.MaxDepth = reader.MaxDepth;
            jTokenReader.SupportMultipleContent = reader.SupportMultipleContent;
            return jTokenReader;
        }
    }
}
```

And here's the implementation we need to decide which specific implementation of ```ISport``` we need to create:

```
using System;
using JsonMapping.Models;
using Newtonsoft.Json.Linq;

namespace JsonMapping.Serialisation
{
    public class SportConverter : JsonCreationConverter<ISport>
    {
        protected override ISport Create(Type objectType, JObject jObject)
        {
            var sportName = (string)jObject.Property("name");
            switch (sportName.ToLower())
            {
                case "squash":
                    return new Squash();
                case "football":
                    return new Football();
                default:
                    throw new InvalidOperationException($"The sport name '{sportName}' cannot be found.");
            }
        }
    }
}
```

It simply reads the ```name``` property from the JSON and decides which object to create from that property name.

This gets wired up in ```WebApiConfig.Register```:

```
serializerSettings.Converters.Add(new SportConverter());
```

To test this I've updated my Person controller Get function to have to Children with different favourite sports:

```
Children = new List<Person>
{
    new Person
    {
        Name = "Tim",
        FavouriteSport = new Squash { CourtName = "Romsey" }
    },
    new Person
    {
        Name = "Kate",
        FavouriteSport = new Football { PitchName = "Winchester" }
    }
}
```

The _GET_ now returns the sports:

```
{
    "name": "Bob",
    "children": [
        {
            "name": "Tim",
            "favouriteSport": {
                "name": "Squash",
                "courtName": "Romsey"
            }
        },
        {
            "name": "Kate",
            "favouriteSport": {
                "name": "Football",
                "pitchName": "Winchester"
            }
        }
    ]
}
```

and I can take the response from that and _POST_ it back to see it mapped and reflected back as expected.

# HTML sanitisation

The final thing I wanted to describe was my need to allow HTML in the model but ensuring that the HTML was safe. In my real-life application I provide rich text editors in the form editor SPA that allow the user to provide quite complex formatting which needs to be preserved but this is also open to malicious injection. Therefore I want a simple way of knowing that any property on my model that allows HTML is safe before my controller action methods are handed the model.

For our example let's create a ```Description``` property on ```Person```:

```
[AllowHtml]
public string Description { get; set; }
```

I've chosen to use the ```AllowHtmlAttribute``` which is included in the ```Microsoft.AspNet.Mvc``` Nuget package which will need installing.

I've chosen to implement a [https://www.newtonsoft.com/json/help/html/SerializationSettings.htm#ContractResolver](ContractResolver) so solve this problem for me and rather than explaining how it works I'm just going to show how's it configured.

In ```WebApiConfig.Register``` I'll replace the existing ```ContractResolver``` property setter with the following:

```
serializerSettings.ContractResolver = new HtmlSanitisingContractResolver();
```

The implementation of ```HtmlSanitisingContractResolver``` looks like this:

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web.Mvc;
using Newtonsoft.Json;
using Newtonsoft.Json.Serialization;

namespace JsonMapping.Serialisation
{
    /// <summary>
    /// Note how this implements CamelCasePropertyNamesContractResolver - it's not the most elegant
    /// way to combine the resolvers but NewtonSoft doesn't provide a better option.
    ///
    /// The HtmlSanitisingContractResolver automatically finds properties on deserializing models
    /// that are decorated with AllowHtml or AllowHtmlAttribute. It then assures that any data
    /// set on the property is sanitised such that any HTML not on a white-list is removed.
    /// 
    /// </summary>
    public class HtmlSanitisingContractResolver : CamelCasePropertyNamesContractResolver
    {
        protected override IList<JsonProperty> CreateProperties(Type type, MemberSerialization memberSerialization)
        {
            var properties = base.CreateProperties(type, memberSerialization);
            foreach (var property in properties.Where(p =>
                p.AttributeProvider.GetAttributes(typeof(AllowHtmlAttribute), false).Any()))
            {
                var propertyInfo = type.GetProperty(property.UnderlyingName);
                if (propertyInfo != null)
                {
                    property.ValueProvider = new HtmlSanitisingValueProvider(propertyInfo);
                }
            }

            return properties;
        }
    }
}
```

As you can see I'm inheriting from ```CamelCasePropertyNamesContractResolver``` at the moment in order to preserve camel casing. This solution is rather poor and I'll implement a more robust design in the future.

The code above is modifying the deserialisation metadata in order to provide a different ```ValueProvider``` for properties on the model that have the ```AllowHtml``` attribute. The value provider looks like this:

```
using System.Reflection;
using Newtonsoft.Json.Serialization;

namespace JsonMapping.Serialisation
{
    public class HtmlSanitisingValueProvider : IValueProvider
    {
        private readonly PropertyInfo _targetPropertyInfo;

        public HtmlSanitisingValueProvider(PropertyInfo targetPropertyInfo)
        {
            _targetPropertyInfo = targetPropertyInfo;
        }

        public void SetValue(object target, object value)
        {
            var sanitisedHtml = new Ganss.XSS.HtmlSanitizer().Sanitize(value as string);
            _targetPropertyInfo.SetValue(target, sanitisedHtml);
        }

        public object GetValue(object target)
        {
            return _targetPropertyInfo.GetValue(target);
        }
    }
}
```

I'm making use of a Nuget package called [https://github.com/mganss/HtmlSanitizer](HtmlSanitizer) which provides the means to sanitise HTML on a white list basis. It is possible to add html/css elements to this white list but for this example I've kept it very simple.

And now test with a _POST_ like this:

```
{
    "name": "Bob",
    "description": "<p>Hi! <script>alert('xss!')</script></p>"
}
```

and notice how the reflected results has removed the javascript from the HTML above:

```
{
    "name": "Bob",
    "description": "<p>Hi! </p>"
}
```

# Summary

That really was a very long post but it covers a lot of aspects of binding to an action method property without having to write lots of code in the method itself. I also see these methods proving very useful for peforming authorization checks such that the user has already been authenticated but now I want to check that data within the posted JSON is authorized for them to access (for example they've posted data which includes a organisation identifier but are they actually a member of that organisation?)

I'm also leaving open the question of how these global integrations are tested since it could be easy to break functionality without actually noticing it's simply no longer configured.