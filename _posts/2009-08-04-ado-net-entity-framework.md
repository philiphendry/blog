---
author: philiphendry
comments: true
date: 2009-08-04 12:39:36+00:00
layout: post
slug: ado-net-entity-framework
title: ADO.NET Entity Framework
wordpress_id: 244
tags:
- entity-framework
---

## Random Thoughts

 

EDM Generator (EdmGen.exe) validates and generates an Entity Data Model (EDM) from an existing database. Mapping will be one-to-one. Available in .NET Framework 3.5 SP1.

 

Available in VS2008 SP1 :

 

  
  * Entity Data Model Wizard
   
  * Entity Designer
   
  * Update Model Wizard
 

Entities are required to have keys. If not the generation tool will infer one (generating a DefiningQuery element in the store schema rendering it read-only until manually confirmed and the element is removed.)

 

Tables representing many-to-many relationships will not be generated as an entity rather a relationship.

 

See [Entity Framework Terminology](http://msdn.microsoft.com/en-us/library/bb387161.aspx).

 

### The Conceptual Model

 

An EDM schema defining entities and associations called the Conceptual Schema Definition Language (CSDL.) Each entity has : a name, a key and a set of properties (of type simple, scalar or complex and can be nullable or have a default value.) 

 

### The Storage Model

 

Uses Store Schema Defintion Language (SSDL) to define the logical model for persistent data. The types used are of those from the storage model (e.g. SQL Server.)

 

### The Mapping Specification

 

Uses Mapping Specification Language (MSL) to connect conceptual types to the storage model.

 

### Architecture

 

The following diagram highlights how EF integrates with ADO.NET Data Providers and where developer interaction occurs. There are three methods for generating queries against the EDM :

 

  
  * Entity SQL
   
  * Language-Integrated Query (LINQ)
   
  * Object query builder methods
 

![Entity Framework Architectural Diagram](http://i.msdn.microsoft.com/Bb399760.07d25f9d-d5bf-43ae-9495-f85b409bf99b(en-us,VS.90).gif)

 

### Working with Entity Data

 

Referenced objects are not automatically loaded and therefore the Load method on the _EntityReference _(for one-to-one relationship) or_ _the _EntityCollection_ (for a one-to-many relationship) must be called to load the related data into the object context. An alternative is to specify a query path that defines the related object to load.
