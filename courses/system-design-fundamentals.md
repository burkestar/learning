# System Design Fundamentals

https://learning.oreilly.com/videos/system-design-fundamentals

Juval Löwy - known in .net community for Windows Communication Foundation (WCF) where “every class is a service” as pre-cursor for microservices

Duration: 3 hours

**Lesson 1: What not to do**

- Problems with functional decomposition
    - Leads to duplication behaviors across services
    - Prevents reuse - argument is that building blocks can’t be reused if they were designed for different parts of the workflow.
    - Example of client that needs to manage workflow between A, B and C.  Suggestion is to chain A → B → C instead to simplify the client, but it complicates the services.
- Architecture and Value
    - Uses analogy about law of thermodynamics
    - Example of functional decomposition of a house (cooking, playing, eating, resting, etc.) doesn’t naturally lead to the architectural layout of a house
    - Domain decomposition of a house (e.g. layout of house by room, which each implies a set of functions that happen within that room).  Gives example of building a house one room at a time (as a milestone), then needing to break things built before to extend it to a new room.
    - Unit testing is borderline useless
- Functional decomposition example
    - Stock trading system
    - Coordinating transactions across services (buy, sell) puts complexity into the boxes (the client?)
    - Basic claim is that functional decomposition is not scalable and will just grow complexity of system over time

**Lesson 2: What to do**

- Decompose based on volatility - “universal principle of good design”
    - Identify areas of potential change
    - Encapsulate in services (this is the basic idea, encapsulate the things that change behind a stable interface)
    - Milestones based on integration not features
    - Volatility is not self-evident and will take effort to figure out (but the assertion is its worthwhile, since it will avoid problems of functional decomposition)
    - “Software industry is like insane asylum” where we keep repeating the same mistakes over again (using functional decomposition) expecting different results.
    - Dunning-Kruger Effect - cognitive bias where unskilled people make mistakes and don’t have the meta-awareness to recognize their mistakes
    - Analogy like “doctors of old” who killed patients by touching them with germy hands and killed people without them realizing it.  Using functional decomposition is the same and nobody wants to admit it.
    - Analogy of house, analyzing volatility
        - At the same customer over time
            - volatility for furniture, appliances, occupants, appearance, utilities
        - At the same time across customers
            - volatility for structure, neighbors, city
        - axes are independent
    - Good system design should be resilient to changes in the customer dimension
    - Identify solutions masquerading as requirements
- Example of volatility analysis
    - Stock trading system
    - Types of users, point of access (web, desktop, mobile), authentication methods
    - System notifications - transport (email, text), services that publish notification, recipients of notification
    - Data storage (local database, cache, cloud) - fundamental requirement is need to store data, where its stored is a solution masquerading as a requirement
    - Type of connectivity and synchronicity (connected and synchronous, queued trades, )
    - Duration of interaction (trade in one session, or spanning multiple sessions and multiple devices)
    - trade item (stock, bond, etc.)
        - frequency of market data updates
        - processing workflow of trade
    - locale (trading rules, UI localization, trade items available)
    - news feed (communication, content, format, frequency)
    - Single service may encapsulate multiple areas of volatility
    - Proposed architecture design has Notifications box and “workflow” boxes for Trades, Analysis, etc. which coordinate the transactions and business logic that functional decomposition would have spread across multiple services.
- Decomposition and business
    - Changes to the nature of business should not be encapsulated
    - Practice.  Can’t learn to ride bicycle from a book.

**Lesson 3: Structure**

- Using layers
    - Layers encapsulate top-down
    - Services within layer encapsulate side-ways (between services in that layer)
- Layers:
    - Client
    - Business (use cases encapsulated in workflows)
    - Resource Access (shared across managers and engines) - expose “business verbs” instead of CRUD that are atomic and can’t be decomposed any further (e.g. credit and debit from bank account)
    - Utilities (common infra to all services)

**Lesson 4: Composition**

- Architecture Validation
    - call chain diagrams and sequence diagrams (captures time and order)
    - architecture must satisfy all use cases (present and future, known and unknown)
    - “architecture is validated when all conceivable use cases are satisfied” (I think he was rejecting this notion as a fallacy)
    - “don’t design against requirements”.  extensive requirements specs with hundreds of use cases should be rejected.
- Design mission
    - there are only a handful of core use cases that capture the essence of the business, all other use cases are a variant of the core use cases
    - identify small set of building blocks (services) to satisfy the core use cases
- Composable design
    - use cases are volatile and will change over time, new ones added, etc.
    - change in use cases causes change in workflow not in different decomposition
    - features are the byproduct of integration of multiple components providing some functionality to the whole
- Containing changes
    - System must respond to changes in requirements
    - Trick is to contain the change, minimize the impacts
    - change in use case means change to workflow (manager implementation but not underlying services)
    - bulk of effort in system goes into engines, resource access, resource, clients, utilities and infrastructure
    - agility is the ability to respond quickly to business changes, which implies the need to contain change, which implies designing atround volatility

**Lesson 5: Design Attributes**

- Volatility and Reuse
    - Design attributes
        - Volatility decreases top-down (this provides stability)
        - Reusability increases top-down
        - Managers should be almost expendable
    - Managers and Engines
    - Open vs Closed Architectures
        - Open Architectures (non-layered)
            - Most flexible, least encapsulated, potential for coupling
        - Closed Architectures (layered)
            - least flexible, most encapsulated, promotes decoupling
            - presenter definitely prefers closed architecture
        - Semi-closed (layered)
            - allows skipping layers top-down, but side-to-side and up are not allowed
            - trades encapsulation for flexibility and performance
        - “escape hatches”
        - Engines are just a strategy pattern with respect to the Manager
    - Design Don’ts
        - Clients should not call multiple managers in single use case (managers are coupled, functional decomposition)
        - managers should not queue calls to more than one manager
        - Law of 0, 1, or ♾️ for how many ways to do something
        - Never queue calls to engines, or resource access
        - do not publish events from engines, clients, resources
        - engines do not subscribe to events
        - engines never call each other
        - resource access never call each other
