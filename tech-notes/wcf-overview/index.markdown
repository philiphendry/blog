---
author: philiphendry
comments: true
date: 2008-03-24 19:05:11+00:00
layout: page
slug: wcf-overview
title: WCF Overview
wordpress_id: 46
---

## Links

  * [www.dasblonde.net](http://www.dasblonde.net)  
  * [www.thatindigogirl.com](http://www.thatindigogirl.com)  
  * [www.idesign.net](http://www.idesign.net)  
  * [.Net Framework 3.0 Community](http://netfx3.com/content/WCFHome.aspx)  
  * Microsoft MSDN [documentation](http://msdn2.microsoft.com/en-us/library/ms735119.aspx)  
  * [Designing Service Contracts](http://www.theserverside.net/tt/articles/showarticle.tss?id=DesignServiceContracts) article 
  * [65 Links](http://www.joeon.net/post/2008/03/Windows-Communication-Foundation---65-Links-to-make-you-an-expert!.aspx) to make you an expert!! (??)  
  * [WCF Syndication](http://msdn2.microsoft.com/en-us/magazine/cc135976.aspx) with a good overview of REST

## Tools

  * Install the Windows SDK to use the Service Configuration Editor(SvcConfigEditor.exe) which helps writing config files. The Service Trace Viewer (SvcTraceViewer.exe) can be used to view log files (for example us the config editor to configure WCF logging then use the viewer to look at the results.) 

## Introduction

Windows Communication Foundation is part of the Microsoft .Net Framework V3.0 and is part of Vista and Windows Server 2008. It's Microsoft's platform for SOA and unifies ASMX, .NET Remoting and Enterprise Services stacks. Because there's a single API it makes it easier to build for a multitude of different circumstances.

SOA simply defines a contract for accessing a particular service and makes it available via some means of communication protocol. WCF abstracts away from particular protocols.

WCF is _interoperable_ since it supports core Web services standards and supports _integration_ since it integrates with earlier technologies such as COM, Enterprise Services and MSMQ.

## Connectivity

Services can be deployed inside a company and connect to TCP/IP endpoints and authenticate to an Active Directory. However, a more common scenario is to support Internet clients either using a Basic Profile for legacy support where credentials are secured through a HTTPS link (_BasicHttpBinding_), or using WS* across HTTP (_WSHttpBinding_) where the message itself is encrypted.

Another option is to deploy into a business partner or for cross-machine communication. In this scenario we may be using a certificate for authentication to a _WSHttpBinding_.

## Basics

A _Service_ must be hosted by a _ServiceHost_ which must specify one or more _Endpoints_ for clients to connect to. A _Client_ must have a _Proxy_ that has an _Endpoint_ that will connect to the service. The service also defines _Behaviours_ that determine, for example, instancing behaviour.

### Example

When a 'WCF Service' item is added to a project the following are automatically added :

  * A reference to _System.ServiceModel_  
  * An _interface_ that defines the contract and is attributed with [_ServiceContract_] on the interface and [_OperationContract_] on each available operation.  
  * A _class_ that implements the contract interface. We could implement more that one contract however!  
  * An_ app.config_ that contains declarations for the service and its endpoints. 

#### Hosting

Hosting relies on the creation of a _ServiceHost_, passing the type that **implements** the service contract, then adding an endpoint to it which declares three things:

  * the type **defining** the contract  
  * the binding type to use  
  * the address to connect to 
    
    using (ServiceHost host = new ServiceHost(typeof(HelloWorld.HelloWorldService)))
    {
        host.AddServiceEndpoint(typeof(HelloWorld.IHelloWorldService),
                new NetTcpBinding(), "net.tcp://localhost:9000/HelloIndigo");
        host.Open();
    }




#### WCF Client




For the client to consume the service it must:






  * Know the contract and in this example has a copy of the interface definition 

  * Create a proxy to the service by using the _ChannelFactory_. The channel has to be defined with the binding and the endpoint address. 

  * Call the service method. 
    
    [ServiceContract(Namespace = "http://www.philiphendry.me.uk/samples/HelloWorld/2008/03")]
    public interface IHelloWorldService
    {
        [OperationContract]
        string TheMessage(string message);
    }
    
    class Program
    {
        static void Main(string[] args)
        {
            IHelloWorldService proxy = ChannelFactory<IHelloWorldService>.CreateChannel(
                new NetTcpBinding(), new EndpointAddress("net.tcp://localhost:9000/HelloWorld"));
            Console.WriteLine(proxy.TheMessage("Hello world"));
        }
    }




Here's the relationship between these objects in a diagram:




[![image](http://philiphendry.files.wordpress.com/2008/03/image-thumb1.png)](http://philiphendry.files.wordpress.com/2008/03/image1.png)




## Web Service Description Language




A WSDL provides:






  * an interoperable contract for clients to discover and use 

  * a description of the service and its endpoints 

  * the bindings and operations 

  * the message and type definitions 

  * policies 



The WSDL can also be used using _svcutil.exe_ to generate the proxy code on the client. Or live meta data exchange can be employed using _WS-MetadataExchange _and _svcutil.exe_ used again.




## Declarative Configuration




A configuration file can be used to define the WCF endpoints. The following example defines two endpoints. The first is in addition to the programmatic endpoint defined in the code above. These second provides the metadata exchange and defines its address as a relative address which must be combined with a baseAddress to form the full path:
    
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <system.serviceModel>
        <services>
          <service name="HelloWorld.HelloWorldService"
                   behaviorConfiguration="serviceBehaviour">
            <endpoint binding="basicHttpBinding"
                      contract="HelloWorld.IHelloWorldService"
                      address="http://localhost:8000/HelloWorld" />
            <endpoint binding="mexHttpBinding"
                      contract="IMetadataExchange"
                      address="mex" />
            <host>
              <baseAddresses>
                <add baseAddress="http://localhost:8000"/>
              </baseAddresses>
            </host>
          </service>
        </services>
        <behaviors>
          <serviceBehaviors>
            <behavior name="ServiceBehaviour">
              <serviceMetadata httpGetEnabled="true"/>
            </behavior>
          </serviceBehaviors>
        </behaviors>
      </system.serviceModel>
    </configuration>




# [](http://11011.net/software/vspaste)Contracts




There are three types of contracts:






  * **Service** - describes the operations a service can perform and maps CLR types to WSDL 

  * **Data** - describes a data structure and maps CLR types to XSD 

  * **Message** - defines the structure of the message between client and server and maps CLR types to SOAP messages 



## Service Contracts




Contracts define the operations and parameter types that are defined on a service. Endpoints can only be associated to a single contract. To define a class and operation must be attributed. It is preferable to attribute an interface then it can be implemented multiple times:
    
    [ServiceContract(Namespace="http://www.philiphendry.me.uk/samples/HelloWorld/2008/03")]
    public interface IHelloWorldService
    {
        [OperationContract]
        string TheMessage(string message);
    }




[](http://11011.net/software/vspaste)




It is a good idea to add the _Name_ parameter to the attributes so that the operation or class name can be changed without affecting the contract. The following example demonstrates this including naming operation parameters and return types:
    
    [ServiceContract(
        Name="HelloWorldService",
        Namespace="http://www.philiphendry.me.uk/samples/HelloWorld/2008/03")]
    public interface IHelloWorldService
    {
        [OperationContract(
            Name="TheMessage",
            Action = "http://www.philiphendry.me.uk/samples/HelloWorld/2008/03/HelloWorldService/TheMessage",
            ReplyAction = "http://www.philiphendry.me.uk/samples/HelloWorld/2008/03/HelloWorldService/TheMessageResponse")]
        [return: MessageParameter(Name="ReturnString")]
        string TheMessage(
            [MessageParameter(Name="Message")]
            string message);
    }




#### ServiceContract Named Parameters






  * _CallbackContract=typeof(<typename>)_ can be specified on the service contract which allows asynchronous calls back to the client against the <typename> for asynchronous messaging. 



#### OperationContract Named Parameters






  * 


_IsOneWay=[true|false]_ can be specified on the operation to make the method more of a fire-and-forget option similar to MSMQ.



  * 


_FaultContract(typeof(<typename>)]_ can be specified to abstract clients away from the details of how a service is written since stack traces in errors specify internal details. The client errors specified in the contract can then be thrown with the following code:
    
    throw new FaultException<MyClientException>(new MyClientException("Custom error"));




[](http://11011.net/software/vspaste)




## Complex Types




The _DataContractSerializer_ is the default WCF serializer and uses the WSDL to examine the contents of the message and converts between the message XML and .Net CLR data types. It can handle:






  * DataContractAttribute 

  * IXmlSerializable 

  * SerializableAttribute 



### SerializableAttribute




Any type with _SerializableAttribute_ will be converted and everything will be serialized including all fields regardless of accessibility. There is no control over naming conventions or data types.




### Data Contracts




The _DataContractAttribute_ can be applied to map between a CLR type and the schema. The _DataMemberAttribute_ must be applied to fields or properties to have them opt-in to serialization.




If an operation includes a parameters or return value that is of a complex type but that isn't serializable then when the service is hosted, the _Open()_ will fail with the error below that indicates that the complex type is not serializable.




[![image_thumb[3]](http://philiphendry.files.wordpress.com/2008/03/image-thumb3-thumb1.png)](http://philiphendry.files.wordpress.com/2008/03/image-thumb31.png)




For example, the following _MessageItem_ complex type is decorated with the _DataContract_ and _DataMember_ attributes and creates a _Nested WSDL_ which contains the definition. The name in the contract will default to the name of the decorated field or property but can be defined by adding the _Name_ named parameter to the attribute.
    
    [DataContract]
    public class MessageItem
    {
        [DataMember]
        public string TheMessage { get; private set; }
    
        public string Category { get; private set; }
    }




[](http://11011.net/software/vspaste)




### Known Types




If a complex type being used as a parameter or return value can be one of a number of derived types, then the _KnownTypeAttribute_ can be applied to the base class to define other types that can be used by the client in conversations with the server. This attribute will have the server generate the contract and WSDL with the extra types included:
    
    [DataContract(Namespace="http://www.philiphendry.me.uk/samples/HelloWorld/2008/03")]
    [KnownType(typeof(ComplexMessageItem))]
    [KnownType(typeof(SimpleMessageItem))]
    public class MessageItem
    {
        [DataMember]
        public string TheMessage { get; private set; }
    
        public string Category { get; private set; }
    }
    
    [DataContract(Namespace="http://www.philiphendry.me.uk/samples/HelloWorld/2008/03")]
    public class ComplexMessageItem : MessageItem
    {
    }
    
    [DataContract(Namespace="http://www.philiphendry.me.uk/samples/HelloWorld/2008/03")]
    public class SimpleMessageItem : MessageItem
    {
    }
    




[](http://11011.net/software/vspaste)




### Service Known Type




The _ServiceKnownType_ attribute can be added to the service contract instead of the actual type since adding to the actual type means that every contract would have to allow all the defined types.
    
    [ServiceContract(Namespace="http://www.philiphendry.me.uk/samples/HelloWorld/2008/03")]
    [ServiceKnownType(typeof(ComplexMessageItem))]
    public interface IHelloWorldService
    {
        [OperationContract]
        string TheMessage(MessageItem message);
    }
    




### Declared Type




Types can also be added to the the configuration file which allows for implementation configuration changes. This can also be adapted to offer dynamically changing types at runtime if required.
    
    <system.runtime.serialization>
      <dataContractSerializer>
        <declaredTypes>
          <add type="HelloWorld.MessageItem, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null">
            <knownType type="HelloWorld.ComplexMessageItem, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <knownType type="HelloWorld.SimpleMessageItem, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          </add>
        </declaredTypes>
      </dataContractSerializer>
    </system.runtime.serialization>




### IXmlSerializable




Is the best way to support a given contract from a object that is very different in structure - the mapping can be controlled explicitly.
    
    [XmlSchemaProvider("GetSchema")]
    public class MessageItemSerializer : IXmlSerializable
    {
        public static XmlQualifiedName GetSchema(XmlSchemaSet schemaSet)
        {
            string schemaString = String.Format("<xs:schema xmlns:tns='{0}' xmlns:xs='http://www.w3.org/2001/XMLSchema' ..... </xs:schema>", ns);
    
            XmlSchema schema = XmlSchema.Read(new StringReader(schemaString), null);
            schemaSet.XmlResolver = new XmlUrlResolver();
            schemaSet.Add(schema);
    
            return new XmlQualifiedName("MessageItem", ns);
        }
    
        public System.Xml.Schema.XmlSchema GetSchema()
        {
            throw new NotImplementedException("Not implemented - the XMmlSchemaProvider attribute specifies" +
                "calling the static GetSchema method instead");
        }
    
        public void ReadXml(System.Xml.XmlReader reader)
        {
            MessageItem = new MessageItem();
            while (reader.IsStartElement())
            {
                if (reader.IsStartElement("TheMessage"))
                {
                    reader.MoveToContent();
                    MessageItem.TheMessage = reader.ReadString();
                    reader.MoveToContent();
                    reader.ReadEndElement();
                }
            }
        }
    
        public void WriteXml(System.Xml.XmlWriter writer)
        {
            throw new NotImplementedException();
        }
    
        public MessageItem MessageItem { get; private set; }
    }
    




## Message Contracts




Message contracts are particularly useful for adding custom headers or controlling protection level by only encrypting certain sections, for example. The message itself is sent as a SOAP method and therefore the SOAP body carries the payload to be given to the client whilst the rest of the message is used for containing, describing and transporting the body. In particular it may be that the header is used by routers to transfer messages to particular servers (load balancing for example) and therefore data marked with _MessageHeader_ will be contained in the header where it will be visible.
    
    [MessageContract]
    public class CustomClass
    {
        [MessageHeader]
        public string operation;
    
        [MessageBodyMember]
        public string data;
    }




## Bindings




Bindings describe how messages are transported including:






  * Transport (HTTP, TCP, Pipes, MSMQ, Custom) 

  * Encoding (Test, Binary, MTOM, Custom) 

  * Security (WS-*, Custom) 

  * Reliability (WS-*, Custom) 

  * Protocol (WS-*, Custom) 

<table cellpadding="2" width="480" border="1" cellspacing="0" >
<tbody >
<tr >

<td width="68" valign="top" >**Binding**
</td>

<td width="68" valign="top" >**Interop**
</td>

<td width="68" valign="top" >**Security**
</td>

<td width="68" valign="top" >**Session**
</td>

<td width="68" valign="top" >**Transx**
</td>

<td width="68" valign="top" >**Duplex**
</td>

<td width="68" valign="top" >**Stream**
</td></tr>
<tr >

<td width="68" valign="top" >**BasicHttpBinding**
</td>

<td width="68" valign="top" >BP
</td>

<td width="68" valign="top" >T
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >
</td></tr>
<tr >

<td width="68" valign="top" >**WsHttpBinding**
</td>

<td width="68" valign="top" >WS
</td>

<td width="68" valign="top" >TS
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >
</td></tr>
<tr >

<td width="68" valign="top" >**WsDualHttpBinding**
</td>

<td width="68" valign="top" >WS
</td>

<td width="68" valign="top" >TS
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >
</td></tr>
<tr >

<td width="68" valign="top" >**NetTcpBinding**
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >TS
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >o
</td></tr>
<tr >

<td width="68" valign="top" >**NetNamePipesBinding**
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >TS
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >o
</td></tr>
<tr >

<td width="68" valign="top" >**NetMsmqBinding**
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >TS
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >
</td></tr>
<tr >

<td width="68" valign="top" >**NetPeerTcpBinding**
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >TS
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >
</td>

<td width="68" valign="top" >y
</td>

<td width="68" valign="top" >
</td></tr></tbody></table>


T = Transport Security, S = WS-Security, O = One-Way only




Bindings are specified in the configuration (although code is possible too) and allow changes without having to recompile code. The following example shows how the endpoint is named _Binding1_ and then the binding properties are modified in the _bindings_ section:
    
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <system.serviceModel>
        <services>
          <service name="HelloWorld.HelloWorldService"
                   behaviorConfiguration="serviceBehaviour">
            <endpoint bindingConfiguration="Binding1"
                      binding="basicHttpBinding"
                      contract="HelloWorld.IHelloWorldService"
                      address="http://localhost:8000/HelloWorld" />
          </service>
        </services>
        <bindings>
          <basicHttpBinding>
            <binding name="Binding1" 
                     hostNameComparisonMode="StrongWildcard"
                     sendTimeout="00:10:00"
                     maxReceivedMessageSize="65536"
                     messageEncoding="Text"
                     textEncoding="utf-8" />
          </basicHttpBinding>
        </bindings>
      </system.serviceModel>
    </configuration>




[](http://11011.net/software/vspaste)




## Behaviours




Behaviours are concerned with system semantics such as:






  * concurrency 

  * instancing 

  * transactions 

  * impersonation 

  * throttling 

  * metadata exposure 



### Instancing




There are a number of instancing behaviours that are available :






  * Per call - a new instance for every call 

  * Singleton - a single instance for all calls 

  * Private session - private instance for each client 

  * Shared session - shared instance across multiple clients 



This can be specified either in code:
    
    [ServiceBehavior(InstanceContextMode=InstanceContextMode.Single)]
    public class HelloWorldService : IHelloWorldService
    {
    }




Or in config:
    
    <system.serviceModel>
      <services>
        <service name="HelloWorld.HelloWorldService"
                 behaviorConfiguration="serviceBehaviour">
            <!-- endpoints -->      
        </service>
      </services>
      <behaviors>
        <serviceBehaviors>
          <behavior name="serviceBehaviour">
            <serviceThrottling maxConcurrentCalls="10"
                               maxConcurrentInstances="10"/>
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>




[](http://11011.net/software/vspaste)
