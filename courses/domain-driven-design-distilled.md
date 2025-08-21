# Domain-driven Design Distilled

https://learning.oreilly.com/videos/domain-driven-design-distilled

Vaughn Vernon, author of DDD books

**Lesson 1: DDD for Me**

- Task-board shuffle - grabbing task from Kanban board and start implementing without much design
- Insidious problems
    - Software development as cost center instead of profit center
    - Throw technology at business problems
    - Priority given to database and data model over business processes and operations
    - Naming objects without business focus (important to tie mental model of business into the software)
    - poor collaboration between developers and stakeholders - “spec divide”
    - project estimates take time and may delay work since it diverts from productive development work
    - “no design” leads to Big Ball of Mud
    - wrong abstractions lead to wrong solutions that miss concrete business needs
    - strong coupling between services make systems difficult to maintain

**Lesson 2: Strategic Design with Bounded Contexts with Ubiquitous Language**

- Ubiquitous Language - spoken by development team, reflected in naming within software
- Bounded Context is the boundary within which a ubiquitous language is used.  Similar to language differences between countries.  Words have specific meaning within that context.
- Find a domain expert for each bounded context
- How to map out domain concepts (boxes with arrows) and then decide how to divide into bounded contexts.  In practice, there will be multiple bounded contexts.
- Come up with a scenario that explains actors (user personas) and their actions, and use that to identify concepts from the bounded context that apply
- Can write scenario as acceptance test scenario for BDD specification (GIVEN/WHEN/THEN)
- Architecture
    - Ports-and-adapters architecture (aka onion architecture)
        - Clients interact with adapter that translates to application service requests within core application’s API.
        - (similar to Microkernel architecture, and “backend for frontend”)
        - may have Input Adapters and Output Adapters
    - Mentions other architecture patterns that DDD can be used with (but without explaining why they are compatible and why others are not)

**Lesson 3: Strategic Design with Subdomains**

- Using subdomains to integrate with legacy systems that aren’t oriented to domain bounded contexts
- Business domain too complex to reason about, divide into logical subdomains (how do these relate to bounded contexts?)
- Core domain is where organization is making strategic investment in software, addressed by highest priority projects
- Generic Subdomain is not core to the business and could be outsourced to 3rd party (e.g. Identify Access)
- Supporting Subdomain is not core to the business but may involve proprietary investment into building out this functionality.
- Dealing with complexity and integrating with a Big Ball of Mud.

**Lesson 4: Strategic Design with Context Mapping**

- Interactions between bounded contexts - team relationships and technical integrations
- Team relationships
    - shared kernel - 2 teams that share parts of core model (code reuse?) - requires a high level of coordination
    - customer/supplier - upstream and downstream relationship.  Upstream changes must be coordinated with downstream consumers, and downstream must request changes for requirements from upstream.  Team 1 upstream can impact team 2’s delivery deadlines.
    - conformist - team 2 downstream will just confirm to team 1’s upstream model if its complex, and consume it as if it were part of its own model (rather than influencing it to change)
    - anti-corruption layer (ACL) - opposite of conformist model - team 2 downstream refuses to be influenced by upstream team and will implement a translation layer.
    - open host service (OHS) - team 1 upstream has well documented API, and team 2 consumes existing API as-is since its well defined and easy to work with.  Doesn’t need translation if using consistent domain model
    - separate ways - team 2 breaks the upstream link on team 1
- Use Anti-Corruption Layer when integrating with Big Ball of Mud upstream to translate and map non-domain concepts to those that are consistent within the downstream team’s services bounded context
- Service Bounded Context use Open Host Service (OHS) and Client Bounded Context uses Anti-Corruption Layer (ACL).  Good for RPC integrations.
- Messaging
    - Aggregate publishes Domain Event available for another subscribing bounded context.
- Context mapping
    - E.g. is Underwriting context that needs to make RESTful call to Policy context using resource ID

**Lesson 5: Tactical Design with Aggregates**

- Aggregate - design small object clusters to manage consistency constraints using transactions
- Aggregate as transactional boundary to control state of its contained entities
- Four rules of thumb
    - Protect business invariants inside Aggregate boundaries
    - Design small Aggregates
    - Reference other Aggregates by identity only
    - Update other Aggregates using eventual consistency
- Example for backlog items on sprint board.  When hours remaining are set to zero, status will be transitioned to done within transaction to protect business invariants.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8f417ad1-1989-4eec-a786-983ab02b15e4/Untitled.png)

- Large Aggregate with a Release and Sprint being planned at same time and violating database constraints when updating.  Break up to smaller Aggregates.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/97d48df2-28ff-4356-bff6-5b93560dbac0/Untitled.png)

- Reference by Identity only (reference to ProductId)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c4989d62-16c1-4acb-981d-9d7166dd26f6/Untitled.png)

- Eventual Consistency to update Aggregates.  When a backlog item is committed to a sprint, the BacklogItem is updated and the Sprint Aggregate also needs to be updated.  A messaging mechanism can send Domain Event from BacklogItem to Sprint Aggregate to update it.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1e359368-d7c2-45f0-90d2-98d4e1ebd046/Untitled.png)

- Problems with overly abstracted model
    - e.g. “Scrum Element” that things inherit from, although it doesn’t belong to a bounded context and is spread across several, increasing coupling
- Right-sizing Aggregates
    - Determine time requirements for consistency of updates.  If immediate, then needs to be part of Aggregate transactional boundary.  If eventual consistent, can be split to separate Aggregate and update via event message.

**Lesson 6: Tactical Design with Domain Events**

- Causal consistency - original domain event triggering derived domain events must have changes applied in order (e.g. QoS guarantee of in-order delivery of messages)
- DomainEvent interface with timestamp.  Derived event classes should use resource IDs for entities affected within the context.
- Domain Events caused by some command or derived based on other state changes (triggered by another event happening)
- Event Sourcing
    - When committing a BacklogItem to a Sprint, the event on the Aggregate leads to a sequence of 3 events that need to be applied in order (in the database) and then the state of the Aggregate instance needs to be synchronized to have the changes applied.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/52feebdc-fe88-4a21-a780-1a0b3d7ec677/Untitled.png)
    

**Lesson 7: Acceleration and Management Tools**

- Event Storming
    - Can be used for knowledge acquisition and lead to modeling insights and breakthroughs.
    - Need the right people (domain experts), sticky notes with different colors, fine tipped marker, some wall surface to put sticky notes
    - Types of sticky notes: Events, Commands, Entity/Aggregate, Policy/Process, View, User Role
    - Step 1: Model Events in Time Order on a time line
    - Step 2: Model Commands that cause Events (Command → Event).
        - Overlap things vertically if events can overlap simulatenously
        - Step 3: Model Aggregates that handles Commands on the domain entities.  Keep the process in time order, so don’t cluster commands for same aggregate together, but duplicate sticky notes so the aggregate commands can occur correctly on the timeline
    - Step 4: identify Context Boundaries.
        - Segment the timeline to group commands and events into some context.  There will be Events that cross the context boundaries.
    - Step 5: Identify Views and other Components.
        - Helps to identify Processes
        - Aggregate receives Commands and emits Events, whereas a Process receives Events and emits Commands.
- SWOT analysis to identify knowledge gaps in the domain model
- Modeling Spikes
    - Needs ongoing refinement after experimentation and things learned along the way
- Modeling Debt
    - Timeboxed modeling efforts will lead to unfinished modeling to refine later.
- Can estimate time to model Event, Command and Aggregate - use Easy, Moderate, Complex
