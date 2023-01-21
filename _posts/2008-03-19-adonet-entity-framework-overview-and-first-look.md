---
author: philiphendry
comments: true
date: 2008-03-19 19:00:32+00:00
layout: post
slug: adonet-entity-framework-overview-and-first-look
title: ADO.NET Entity Framework Overview and First Look
wordpress_id: 43
tags:
- dev-tools
---

I haven't really taken a serious look at the Entity Framework yet so here are just a few notes I've jotted down to get myself started - nothing ground-breaking I'm afraid! I think my goal here will be to establish terms and a baseline view of what's provided.

## Tools

  * Microsofts [overview](http://msdn2.microsoft.com/en-us/library/aa697427(VS.80).aspx)

## The Entity Data Model

The ADO.NET Entity Framework is an implementation of Microsoft's Entity Data Model which supports mapping to relational schemas.

### Entities

An instance of an _EntityType_ has properties which define the entity - for example, a Person entity will have name, birthdate, height etc.) The entity must also specify _Key_ properties which define its uniqueness. _Inheritance_ can be employed to build on an extend previous entities.

### EntitySet

Entities can exist within an _EntitySet _with one or more of them defined inside an _EntityContainer_.

### Relationships

Relationships are defined by an _AssociationType_ which defines the entities in the association, the _Roles_ of each and their _Cardinality_ (one-to-one, one-to-many or many-to-many.) Multiple relationships are contained in _RelationshipSets._

## The ADO.NET Entity Framework

The ADO.NET Entity Framework allows you to define and program against an Entity Data Model and comprises an _EntityClient_ that exposes a storage schema and a set of _ObjectServices_ that exposes the Entity Data Model as strongly typed Business Objects.

### EntityClient Data Provider

An _EntityClient Data Provider_ accepts _EntitySQL_ and adapts it to the underlying storage and therefore provides a common command syntax across many different stores of data

### Conceptual Schema

The _C-Space _describes the schema in terms of the Entity Data Model and is typically loaded from an XML file or stream following the Conceptual Schema Definition Language (CSDL.)

### Storage Schema

The _S-Space_ defines the schema of the relational store - following the Storage Schema Definition Language (SSDL.)

### Entity Mapping

The _C-S Map_ describes the mapping between the conceptual and storage schemas following the Mapping Schema Language (MSL.)

### Object Services

_ObjectServices_ map business objects to the Entity Data Model defined by the CSDL and support querying through _EntitySQL_ and _LINQ_.
