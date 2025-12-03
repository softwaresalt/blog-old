---
title: "Entity Framework Assessment"
date: 2012-07-04
layout: single
categories:
  - Technical
tags:
  - .NET
  - Assessment
---

# Entity Framework Assessment

The Entity Framework is an ORM (object relational mapping) tool. There are other ORM tools on the market as well with different strengths and weaknesses.  Microsoft has ventured into this field with the intent of enabling developers to be more productive with ORM tooling and a framework that are well integrated with their Visual Studio IDE and the .NET Framework.  In the [ADO.NET Entity Framework Overview](http://msdn.microsoft.com/en-us/library/aa697427(v=vs.80).aspx), the introduction describes it as follows:

When one takes a look at the amount of code that the average application developer must write to address the impedance mismatch across various data representations (for example objects and relational stores) it is clear that there is an opportunity for improvement. Indeed, there are many scenarios where the right framework can empower an application developer to focus on the needs of the application as opposed to the complexities of bridging disparate data representations.

A primary goal of the upcoming version of ADO.NET is to raise the level of abstraction for data programming, thus helping to eliminate the impedance mismatch between data models and between languages that application developers would otherwise have to deal with. Two innovations that make this move possible are Language-Integrated Query and the ADO.NET Entity Framework. The Entity Framework exists as a new part of the ADO.NET family of technologies. ADO.NET will LINQ-enable many data access components: LINQ to SQL, LINQ to DataSet and LINQ to Entities.

This document describes the ADO.NET Entity Framework, what problem spaces it is targeting and how its various components address those problems.

## Where We Want To Go

An ideal environment for creation of business applications should allow developers to describe the business logic and state of the problem domain which they are modeling with minimum or no "noise" coming from the underlying representation and the infrastructure that supports it. Applications should be able to interact with the stores that maintain the persistent state of the system in the terms of the problem domain; specifically in the terms of a conceptual domain model, completely separated from the logical schema of the underlying store.
This means that the Entity Framework is, like F# and other higher level languages, an attempt to further abstract software development so that the developer can focus more on the application domain and less on the actual plumbing typically intrinsic to software development.  This is intended to increase productivity essentially by not having to develop a whole bunch of code that you would have to do otherwise.  Accelerators such as the System.Web.Security namespace with the Membership class also fall into this category.

### What is the Entity Data Model (EDM)?

The EDM is a way of conceptually modeling the data that your application will use as described on [MSDN](http://msdn.microsoft.com/en-us/library/ee382825.aspx):

The Entity Data Model (EDM) is a set of concepts that describe the structure of data, regardless of its stored form. The EDM borrows from the Entity-Relationship Model described by Peter Chen in 1976, but it also builds on the Entity-Relationship Model and extends its traditional uses.

The EDM addresses the challenges that arise from having data stored in many forms. For example, consider a business that stores data in relational databases, text files, XML files, spreadsheets, and reports. This presents significant challenges in data modeling, application design, and data access. When designing a data-oriented application, the challenge is to write efficient and maintainable code without sacrificing efficient data access, storage, and scalability. When data has a relational structure, data access, storage, and scalability are very efficient, but writing efficient and maintainable code becomes more difficult. When data has an object structure, the trade-offs are reversed: Writing efficient and maintainable code comes at the cost of efficient data access, storage, and scalability. Even if the right balance between these trade-offs can be found, new challenges arise when data is moved from one form to another. The Entity Data Model addresses these challenges by describing the structure of data in terms of entities and relationships that are independent of any storage schema. This makes the stored form of data irrelevant to application design and development. And, because entities and relationships describe the structure of data as it is used in an application (not its stored form), they can evolve as an application evolves.

A _conceptual model_ is a specific representation of the structure of data as entities and relationships, and is generally defined in a domain-specific language (DSL) that implements the concepts of the EDM. [Conceptual schema definition language (CSDL)](http://msdn.microsoft.com/en-us/library/bb399292.aspx) is an example of such a domain-specific language. Entities and relationships described in a conceptual model can be thought of as abstractions of objects and associations in an application. This allows developers to focus on the conceptual model without concern for the storage schema, and allows them to write code with efficiency and maintainability in mind. Meanwhile storage schema designers can focus on the efficiency of data access, storage, and scalability.

Note that the _challenge_ is to create an efficient framework that allows the application developer to define a conceptual model of entities that defines the business domain s/he is working in and which gives context to the data and to do so in a way that does not compromise one side or another of the mapping equation. A key point here in any ORM framework is to map a logical entity or business object to relational storage structure.

### What are the different ways of applying Entity Framework as an ORM?

As an ORM, EF4 lets you design your data model in one place either by going the Code First route or the Entity Model route. EF5 should allow developers to also go the route of database first.

#### Code First:

With Code First, you model your data structures in C# code where your business domain resides in terms of logic, business rules, and functionality. The Entity Framework can generate the Entity Data Model from this design, and then from this generate the physical persistence model as a SQL schema.

#### Model First

With the Entity Model, you design your data structures conceptually and the Entity Framework generates the C# code to encapsulate the data model as classes and the physical persistence model as a SQL schema.

In both cases, the Entity Framework is performing what is known as projection in the ORM world.  Projection is a way of managing your model in one place and letting tooling generate the code used on all sides of that model. This is supposed to increase productivity because you are able to centrally manage your entity model.  This does, however, mean that you are now depending on code generators, which have a long and troubled history.  Code generators can be something of a Rube-Goldberg machine in their attempts to simplify the lives of software developers.  Furthermore, they tend to produce code that is rigid to the template rather than being organic to the problem domain.

### What is the purpose of the Entity Framework as an ORM?

[The point of using the Entity Framework as an ORM](http://weblogs.asp.net/fbouma/archive/2008/05/19/why-use-the-entity-framework-yeah-why-exactly.aspx) is to achieve greater productivity, lower maintenance, and simplify development.  That being said, there are some principles behind why you would use an ORM in the first place.

First, let's examine the problem domain that using an ORM is intended to address:

Let's say you are designing an application and it has a bunch of business initiatives it is supposed to encompass. The architect decides to model the business domain as a set of entities that embody all of the _things_ that will be acted upon.  In an ORM, these logical entities would then be projected into an object model in code which can then be coded against by the team as they develop all of the business functionality.  The ORM also provides the data access layer that maps the object model to the relational representation of the logical entities designed by the architect.  The promise of an ORM is that the team or individual(s) can start delivering business functionality quicker by focusing almost entirely on the business domain.  This addresses the problem domain of how to get an application stood up as quickly as possible and allow a business to respond quickly to customer and stakeholder needs.

Next, let's examine how this is accomplished with EF:

EF allows developers to focus on the business functionality without having to worry about the data access layer by abstracting out the data access layer as a service layer to the database.  There is a lot of precedence for this, such as the TCP/IP layer in Windows; I don't need to develop code to access the network within my application, I just build an application that utilizes that service layer of Windows and focus on my problem domain.

This approach can also greatly benefit a business in light of the reality that a majority of applications don't last very long.  If the app has a statistically low probability of surviving long term, why invest too much money into functionality until you are sure it's going to sustain value?

Next, let's focus again on the problem domain, but with an emphasis on EF:

The Entity Framework addresses the problem domain by creating a mechanism for modeling entities and then projecting code into all of the other domains. One can model in code first and then project into the entity model and the database or model the entities and project code into the object model and the database.  With EF5, one should also be able to model in the database, and then project forward into the entity model and the object model.  This means the code generation is in play in whichever direction projection is occurring. It must be emphasized that the Entity Framework is actually generating SQL DDL if modeled in code first or in the entity model.  EF is also generating  SQL DML code in the data access layer it generates when it fetches from and saves to the database.  This is the scenario in which the Entity Framework is designed to achieve the most value.

### Reservations about the Entity Framework

Let's follow the scenario in which a large database already exists and you want to rapidly build a Web application using the EF.  What is the problem domain you are trying to solve by using the EF? On the surface, it seems obvious: productivity.  However, what about the cost of maintenance within the context of [software evolution](http://en.wikipedia.org/wiki/Software_evolution)? It would be inadvisable to maintain the existing database schema through the EF for a number of reasons.

1. The [separation of concerns](http://en.wikipedia.org/wiki/Separation_of_concerns) applied to this scenario means that how you represent information in a relational model will be different from how you model it from a business point of view as materialized in an object model. The EF projects DDL according to the model point of view from which you are designing.  This means that if you design an entity from a business point of view, it will project a table schema that matches that design even though that is not how you should model the data from a relational point of view.  This inordinate focus on the data aspect of entities and other concerns elicited a [vote of no confidence](http://efvote.wufoo.com/forms/ado-net-entity-framework-vote-of-no-confidence/) for the EF.

1. If the database supports multiple systems and/or applications, it is not practical to manage the schema of a database entirely through the lens of one its consumers.  Its schema should be managed through source control using an independent mechanism that allows you to perform database builds and deployments outside of the domain of the consuming application and its builds and deployments.

1. The aforementioned analogy of the Windows TCP/IP service layer is flawed by the fact that while the separation of concerns works as it should in the case of the TCP/IP layer in the Windows service stack, it does not in the case of the EF data access layer from an optimization point of view.  The TCP/IP layer in Windows specializes in what it does and provides an optimal service for systems that use it.  EF, on the other hand, provides a data access layer that does not provide an optimal service layer.  In fact, it is not intended to provide optimal performance; it generates DML that is frequently less than optimal.  There is also no guarantee that it will effectively batch requests and saves to the database, so it can become very network chatty.

Let's perform another thought experiment and imagine that we want to develop a custom web application that will have a dedicated database just for itself. In this scenario, the EF seems like a good fit for the following reasons:

1. We can use the EF as an ORM as it is intended.
1. We can model our entities and project into our object model and our relational model as the EF was designed to support.
1. We can centrally manage all of the data models and achieve lower maintenance of the system while driving greater productivity and focus on the business domain of the solution.

So, what could be wrong with using the EF as an ORM in this situation?

1. Looking at the ORM approach to abstracting the data access layer, there are some [anti-patterns](http://www.ben-morris.com/entity-framework-anti-patterns-how-not-to-use-an-orm-with-sql-server) intrinsic to using the EF because of how it generates its own DAL. An issue with using a generated DAL is that it artificially paints a software development landscape in which the developer doesn't need to know what is going on at the database level, nor need to understand how the system works end-to-end.  This is a dangerous illusion. Software developers should not limit their skills and knowledge to a single development domain; they should understand the business of the software they are writing and the majority of the technical context in which it operates.  It is very important as a software developer to know how your code is secure and how it is performant, and how to solve performance problems in your software design.

1. Looking at it from an Object Oriented point of view, the [criticism of the EF](http://codebetter.com/iancooper/2008/06/26/the-criticism-of-the-entity-framework-is-not-just-around-domain-driven-design/) encompasses the issues of domain driven design and how to design objects from a business point of view as a separate concern from how that data will be stored. Modeling entities to represent data from an object point of view is a separate concern from how to represent that data in a relational system.  The EF will project the model designed in the Entity Model into the relational database domain.  It is important for a developer to understand the difference between how data should be represented in different technical domains and abstracting those domains away from the developer effectively removes critical understanding and control of those systems and how they interact.

1. Another aspect of this is that because the EF generates the DAL based on the Entity Model and/or from reverse engineering tables from the database, everything is based on direct access to tables.  Views and stored procedures are not the primary means of interfacing with the database.  Views are not fully functional for the same reason that while you can insert to and update views, there are limitations, especially where the view contains joins in the query.  Views and stored procedures are typically the best way to provide interfaces to the database and provide optimized SQL code for use by an application.  However, the only way to use stored procedures is to add them as a function to the Entity Model.  This means that the use of those are [not represented in the entity model](http://www.kindblad.com/2009/01/11/why-you-should-not-use-the-adonet-entity-framework/) as part of the use of the entities. From an abstraction point of view, one should design entities from a business point of view and then operate on those entities/objects within the application domain, fetching and saving from that point of view. On the mapping side of things though, one should be able to optimize these operations to use stored procedures or not regardless of how the objects are used.  But in the EF, use of stored procedures breaks that paradigm because [the mapping is not as flexible](http://blogs.msdn.com/b/dsimmons/archive/2008/05/17/why-use-the-entity-framework.aspx) as other tools such as nHibernate.  This means that use of stored procedures is discouraged by design and only supported for cases when performance considerations require it.

### Opinion

The top 10 reasons I would not use the EF:

1. I typically work on all parts of an application from the database to the UI. My experience is that it is critical to understand solid design principles that affect performance and to understand how all parts of a system interact.  Solving performance problems and general troubleshooting can be very difficult without  fully  understanding how a system works.

1. I don't like code generators in general, and the EF generates code even though it doesn't bill itself as such.  I've seen multiple systems that had legacy code originally generated to help accelerate development of an application/system and stand it up.  In every case, the generated code ended up becoming technical debt.

1. I prefer to have granular control over code because I can impose good design decisions over objects and the granularity of communication where different parts of the system are distributed.

1. I don't like how the EF has to access tables directly and pretty much can't operate as it was intended without that type of access.

1. The EF is not extensible, so if you want to give yourself greater control over how it functions, you're out of luck.

1. The EF, in my opinion, significantly bloats the size of an application's code base.

1. I've seen generated DAL code reused in multiple areas of an application, and it became very problematic because while one important concept of creating objects is reusability, how something is reused can vary.  In this case, the DAL code forced all code that used it to run at a specific transaction level regardless of the context in which it was being used.  I'll concede though that with the EF, one is able to set the transaction level.  Nevertheless, the rigid, templated nature of it lends itself to inflexibility and therefore less reusability.

1. I especially eschew the generation of an entity model by reverse engineering it from the database.  This methodology produces convenience at the expense of good practice, and I've never seen convenience win that argument in the long run.

1. I find it ironic that the [Entity Framework is Microsoft's recommended data access technology for new applications](http://msdn.microsoft.com/en-us/data/ef.aspx) considering that it [deemphasizes the use of stored procedures](http://blogs.msdn.com/b/dsimmons/archive/2008/05/17/why-use-the-entity-framework.aspx), one of MS's signature advantages in SQL Server over other DBMS. There are religious camps asserting that the use of stored procedures is terrible because all business logic should reside in the application domain and that stored procedures create enormous impedance to changing DBMS depending on costs and needs.  While this is true to some extent, foregoing the use of stored procedures comes at significant performance cost as well as the ridiculous requirement to embed large chunks of SQL code into the application domain and pass it through to the database regardless of the security concerns inherent in that approach.  I think ADO.NET and stored procedures work just fine and provide much greater control and security.

1. And speaking of choosing technologies to provide greater flexibility so that you are not overly dependent on any specific subsystem, such as the RDMS: once you've locked yourself into using the EF for your application, it is prohibitively expensive to unwind it if you realize that it is just not technically flexible enough for your needs or that it doesn't support very well what you are doing out of the box.  Once you realize that, you're SOL because just like trying to customize any out-of-the-box software to meet your specific problem domain, you are in a situation where you will expend enormous effort trying to extend or customize something for your needs.  I have seen this before, and this is why I would make every effort to avoid using a technology that is inflexible or not extendable or which is simply not designed to solve the specific problem domain you are attempting to address.

So whatever you do, if you want to use the EF, first make sure it is the right technology for the job and meets the challenge of addressing the specific problem domain you are tackling.  I'm sure there are some valid use cases for it; I just haven't encountered them yet.
