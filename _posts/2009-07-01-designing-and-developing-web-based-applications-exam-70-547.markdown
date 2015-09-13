---
author: philiphendry
comments: true
date: 2009-07-01 18:40:59+00:00
layout: post
slug: designing-and-developing-web-based-applications-exam-70-547
title: Designing and Developing Web-Based Applications (Exam 70-547)
wordpress_id: 225
tags:
- notes
- Exam
---

# Chapter 1 : Application Requirements and 

 

# Design

 

Before any requirements gathering takes place a feasibility study will probably be performed.

 

Any application development starts with a _**Vision**_ defined by the project stakeholders and management. Architects and developers often help business analysts to define the goals and a full set of requirements.

 

**_MSF (Microsoft Solutions Framework)_** for Agile Software Development defines 

 

  
  * a _**QOS (quality-of-service)**_ requirement as a work item allowing for performance, load, availability, stress, accessibility, serviceability, and maintainability requirements. 
   
  * a **_Scenario_** work item representing a use case – a path of user activity through the system to reach a specific goal. 
 

**_MSF for CMMI (Capability Maturity Model Integration)_** defines a **_requirement_** work item with a number of subtypes that can be sub-grouped:

 

  
  * user requirements with type scenario 
   
  * non-functional requirements with types quality of service, safety, security 
   
  * functional requirements with types functional, operational, interface 
 

## Categories of Requirement Definition

 

Requirements must be defined from _multiple perspectives_ capturing all the needs across the different types of stakeholders. There are a limited number of tags:

 

  
  * **_business requirements_** – typically defined as _high-level_ requirements by management or stakeholders and define a vision or goal for the system and therefore need to be translated into tangible requirements. 
   
  * **_user requirements_** – the tasks the users must be able to accomplish to meet the objectives of their jobs. As _high-level_ requirements they are probably defined as one-liners for example - ‘A customer service representative must be able to place an order for a customers.’ A _use-case_ details the how and a _specification_ provides developers with the information they need. 
   
  * **_functional requirements/specifications_** – are features the developer must build to satisfy the other requirements. Often better defined through application modelling tools to prevent documenting them once on paper and again in the models. 
   
  * **_quality of services_** – define contractual, non-functional requirements of the system such as performance, scalability, and standards. 
 

## Use Cases and Requirements

 

Typically both Use Cases and Requirements are required for a successful project. 

 

_**Requirements**_ define what needs to be created for the system to be successful and are useful for:

 

  
  * defining scope 
   
  * determining objectives are met 
   
  * provide traceability throughout the project lifecycle (traceability matrices) 
 

**_Use Cases_** define a set of steps to reach a common user goal and contribute to reaching a requirement.

 

## Evaluating the Requirements

 

To determine whether the defined requirements are sufficient there are a number of questions that can be asked :

 

  
  * **_Feasible_** – are the requirements feasible against know constraints such as budget, timeline, and technology? 
   
  * **Unambiguous**_ _– Does each requirement define a specific, actionable scope item? Can each requirement be acted upon? No soft requirements such as ‘The application should be easy to use’ which is simply goal. 
   
  * **_Necessary_** – do we need all the requirements to complete the system? 
   
  * **_Complete_** – is further clarification required? Are there missing requirements? 
   
  * **_Requirement perspectives_** – have the four categories of requirements been covered? 
   
  * **_Actionable_** – is there enough detail to proceed with the requirement. 
   
  * **_Measurable_** – can the requirement be measured to understand when it is complete or progress is being made? 
 

## Recommending Technologies

 

Technology choice should be driven by the requirements. Make it a requirement to use existing technologies if this is valid and justified. A choice of one technology over another should be evaluated against the requirements.

 

Architecture and technology recommendations are mutually exclusive and should be defined separately. The book suggests technology should be defined first and then combined with the requirements to drive the architecture definition – although I’m not sure that’s strictly the best way.

 

There are a number of layers that can be considered to break down the technology choice :

 

  
  * **_Client_**              
    * _standard browser-based client_
       
    * _AJAX-enabled client - _(highly interactive/useable possibly having to sacrifice browser-compatibility) 
       
    * _smart client – _a client deployed through a web browser but runs as a Windows application. 
       
    * _Microsoft Office client_
       
    * _Windows Mobile_
       
   
  * **_Third-Party Controls_**
   
  * **_Application Server_** – will need to consider the version of IIS and .NET framework. 
   
  * **_Application Libraries_** – _frameworks_ and _libraries_ are slightly different. Frameworks build on libraries by offering more architectural ‘plumbing’. 
   
  * **_Security_** – this should include logging on but also any necessary security of data that is required (e.g. encryption.) 
   
  * **_Data Storage_**              
    * _File-based storage_ – for example CSV or XML. 
       
    * _Microsoft SQL Server 2005 Express Edition_ – limited to a single CPU, 1Gb of RAM and a 4Gb database. However it also offers a file-based embedded option. 
       
    * _SQL Server Everywhere Edition – _also _SQL Mobile_ provides a small lightweight database for handheld devices. 
       
    * _SQL Server editions and options_
       
    * _Other data storage_ – for example Oracle or DB2. 
       
 

### Existing Options

 

Don’t forgot pre-built options that can save time, money and testing:

 

  
  * **_Corporate assets_** – anything already built by the company 
   
  * **_Third-party components_**
   
  * **_Microsoft Windows SharePoint Services_** – services for creating collaborative portal solutions using .Net Framework 2.0 to build Web Parts. 
   
  * **_Microsoft Commerce Server_** – a platform for building business applications that centr on e-Commerce and specifically works with ASP.NET 2.0, BizTalk Server, SQL Server, SQL Server Reporting Services and others. 
   
  * **_Microsoft BizTalk Server_** – manages business process and integration. BizTalk 2006 integrates with VS2005. 
   
  * **_Microsoft Host Integration Server_** – provides integration with IBM mainframes by connecting to data sources, messaging and security. 
   
  * **_Microsoft SQL Server SSIS (SQL Server Integration Services)_** – provides functionality to extract, export and transfer. 
   
  * **_Microsoft SQL Server Reporting Services_** – provides SQL-based reporting from either an OLAP database or OLTP. 
   
  * **_Microsoft SQL Server Analysis Services_** – converts data to a queryable cube. 
 

## Creating a High-Level Application Design

 

At this point there will be:

 

  
  * A set of evaluated and confirmed requirements 
   
  * A technology recommendation based on the requirements 
 

The high-level application design will explain the application intended to be created and therefore will define the technologies to be used and how they’ll be connected.

 

Typically this seems to be envisaged by Microsoft as an application diagram created in Visual Studio that highlights the different areas in the system (e.g. web UI, office integration, web services, application services) and the constraints that govern their configuration (e.g. the version of Windows and IIS ASP.NET is installed on.)

 

# Creating Proof-of-Concept Prototype

 

Design might follow the High-Level Application Design but often it is necessary to produce a prototype to mitigate risk due to requirements missing detail and answer any remaining questions. There should be an expectation that a prototype may feedback on the requirements and change them.

 

Ultimately a prototype will result in a presentation to the stakeholders and re-enforce the usability, estimated effort, technology recommendation, design challenges and the missing or poor requirements. 

 

There are a number of different types of prototype:

 

  
  * UI prototype 
   
  * architecture prototype 
   
  * feasibility study (for example for BizTalk or Commerce Server) 
 

However, there are only two categories of prototype:

 

  
  * **_Mockup_** (also**_ horizontal prototype_**) – take a single, horizontal picture through the system and often help to validate use cases, navigational structure and logical interactions. However, they don’t prove any of the architecture or technology decisions. 
   
  * **_Proof-of-concept_** (also **_vertical prototype _**and **_reference architectures_**)**_ _**– validates requirements and confirms the technology recommendations and high-level design usually through all the layers (UI, services, business objects, and database.) Typically a riskier requirement is chosen in order to mitigate any risk further down the development cycle. 
 

Key to a prototypes success is that it answers questions or risks arising from the requirements. For example, there are the following risks:

 

  
  * **_Confirm a server-side versus client-side experience – _**this might for example prove that the added complexity of javascript for client-side validation is worthwhile due to the reduce server trips.
   
  * **_Confirm the application container_** – this relates to the master-page or MDI style UI design that dictates the way all other UI elements are brought together in the final solution.
   
  * **_Define user interface elements_** – classifying the screens required in the system help to understand the scope of the system. Once classified their complexity can be defined in terms of functionality (read/write), the number of elements, the user interactivity, and the access to and from the screen. There are a limited number of screen types:
        
    * **_data entry form_**
     
    * **_data list form_**
     
    * **_wizard_**
     
    * **_report             
_**
      
  * **_Evaluate web service recommendations_** – consider:
        
    * how will users be connected?
     
    * how will the application behave if the connection is lost or is slow?
     
    * how will transaction reliability be managed?
     
    * will all calls be synchronous?
     
    * how will concurrency be managed?
     
    * will the calls require securing and how?
      
  * **_Evaluate the proposed security model_** – consider:
        
    * **_feasibility_** – for example do all users have accounts and do all target browsers support the authentication type?
     
    * **_authentication_**
     
    * **_authorization_** – at what level(s) will the authorization occur?
     
    * **_access rights to resources_** – how should internal application elements be secured (e.g. database connection string.)
     
    * **_connectivity between resources_** – are there firewalls to consider?
      
  * **_Evaluate third-party applications or controls_**
   
  * **_Evaluate proposed data access and storage methods_**
   
  * **_Evaluate your state management decisions_**
   
  * **_Confirm and refine the recommended architecture – _**this might include confirming a proposed new framework fulfils its purpose. This would also prototype the layering proposed in the architecture. 
 

# Chapter 2 : Decompose Specifications for Developers

 

## Creating a Logical Model

 

The logical is the first step to bridging the gap between documented requirements and a finished application and represents the logical objects, attributes, relationships, and constraints of your domain. The logical model is applied against the technology recommendations and target architecture to create developer specifications (a physical model.)

 

Logical models should remain fixed unless they’re wrong (!) or the real world concepts, on which they’re based, change. Logical models are independent of architectures and technology.

 

### Object Role Modeling

 

Entity-Relationship diagrams tend to be primarily associated with databases, and class diagrams are associated to physical, object-oriented development. ORM diagrams are purely logical and allow users, business analysts, and developers to all understand the domain.
