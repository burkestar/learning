# Monolith to Microservices

## Overview

As a follow-on to his 1st book - [Building Microservices](https://samnewman.io/books/building_microservices/) - this book has practical advice for engineering teams that currently have a monolithic system and are interested in whether migrating to microservices is worth the effort, and some useful strategies and patterns that can be used on the journey.

[Book listing on author’s site](https://samnewman.io/books/monolith-to-microservices/)

---

## Chapter 1 - Just enough Microservices

What are microservices?

- Independently deployable services
    
- services modeled around business domains
    
- Services communicate with each other via networks
    
- Type of Service-Oriented Architecture (SOA)
    
- Expose business capabilities that they encapsulate via one or more network endpoints
    
- Encapsulate data storage and retrieval, exposing data, via well-defined interfaces
    
    - Databases are hidden inside the service boundary
        

Conway’s law:

- “Any organization that designs a system…will inevitably produce a design whose structure is a copy of the organization’s communication structure”
    

Software needs are changing:

- Ship software ever faster
    
- Poly-skilled teams, reduce hands and silos
    

Organizing architecture around business domain to make it easier to deliver changes and organize teams around business needs

Creates problem for network latency as a result of additional network hops and distributed system architecture

Team will face challenges related to distributed systems

“Rightsizing microservices”

- As small of an interface as possible
    
- Size doesn’t matter as much as operational considerations.
    
- How many microservices can you handle? More services, more complexity.
    
- How to define the boundaries to get the most out of the decoupling and benefit from independently deployability, etc.
    

Types of monolith

- Single process monolith
    
    - 1 artifact, 1 deploy, 1 running process
        
    - simpler deployment
        
    - avoids challenges of distributed systems
        
    - simpler developer workflow
        
    - simplified end-to-end testing, monitoring, etc.
        
    - simplified code reuse (just import it)
        
    - valid choice for certain business contexts! (especially recommended for startups where service boundaries are not yet clear)
        
- Modular monolith
    
    - 1 process, multiple modules
        
    - Benefit from clear code ownership, simpler deployment and operations by avoiding distributed system challenges
        
    - Database may still be coupled, making it harder to migrate to microservices in the future
        
- Distributed monolith
    
    - multiple services, 1 deploy
        
    - disadvantages of distributed systems and single-process monoliths
        

Delivery contention

- confused lines of ownership
    
- confusion around who owns what, and who makes decisions
    

Coupling and cohesion

- Coupling - how changing one thing requires a change in another
    
- Cohesion - how we group related code
    
    - “code that changes together, stays together”
        
- “Structure is stable if cohesion is high and coupling is low” - Larry Constantine
    

Types of coupling

- implementation coupling - when implementation of B changes, A also changes.
    
    - example is database schema by 2 consumers.
        
    - extract stable interface
        
- temporal coupling - synchronous calls in a distributed environment, which requires all services called to be available for the whole operation to complete.
    
    - caching can help
        
    - asynchronous transport (message broker)
        
- deployment coupling - requires full deploy of all components of the system
    
    - reduce the risk of each deployment by reducing scope of deployment (only change what needs to be changed, less to go wrong)
        
    - smaller batch size of changes, easier to determine what went wrong and fix it.
        
    - fast feedback and release-on-demand methods from Continuous Delivery
        
- domain coupling - required interaction among business processes to deliver a service (e.g. order processing, warehouse, fulfillment and shipping)
    

Domain-Driven Design

- Aggregate
    
    - collection of objects that are meaningful to the business (e.g. Order, Invoice, Stock Item)
        
    - representation of real domain concept
        
    - have a lifecycle around them (implemented as state machine)
        
    - self-contained units that handle their own state
        
- Bounded Context
    
    - represents a larger organizational boundary inside an organization
        
    - represents the different perspectives related to aggregates based on some context (e.g. how an item is treated within Order system, Warehouse as inventory, Invoicing for payment, Fulfillment, etc.). There are different aspects (i.e. properties of that item, maybe columns in the database?) of the aggregate that matter.
        
    - hide implementation details for how that service is carried out by the business
        
    - represents a collection of one or more associated aggregates
        

Starting out:

- Reduce number of services you work with
    
- Split services around aggregate boundaries
    

---

## Chapter 2 - Planning a Migration

---

## Chapter 3 - Splitting the Monolith

---

## Chapter 4 - Decomposing the Database

---

## Chapter 5 - Growing Pains

---

## Books referenced

- Building Microservices
    
- Continuous Delivery
    
- Release It!
    
- Refactoring
    
- Working Effectively with Legacy Code
    
- Domain-driven Design
    
- Enterprise Integration Patterns
    
- Designing Data-Intensive Applications
    
- Refactoring Databases
    
- Distributed Systems Observability