# Principles of Microservices

https://learning.oreilly.com/videos/the-principles-of/9781491935811

Sam Newman

~2.75 hrs

**Introduction**

- What are microservices?
    - Definition: “small, autonomous services that work well together”
    - Characteristics:
        - Independent processes
        - communicate over APIs
        - high degree of autonomy
        - small, focused on doing one thing well
- Service-Oriented Architecture (SOA)
    - Microservices are a very specific implementation of SOA, but incorporating lessons over time for how to do them well
- Advantages of microservices
    - Better alignment within the organization.  Ability to add new development teams and scale with rapid growth.
    - Release functionality faster
        - Ability to deploy changes faster and with less risk (as compared to monolith) since fewer areas are affected.  Shipping more frequently (since running fewer tests) leads to smaller batch sizes too, and less deployment risk.
    - Independent scaling
    - Scaling Cube (recommended book: [Art of Scalability](https://www.amazon.com/Art-Scalability-Architecture-Organizations-Enterprise/dp/0134032802))
        - Horizontal Duplication - scale out services, such as with load balancer
        - Data Partitioning - scale out data across multiple nodes
        - Functional Decomposition - break apart functions to be apply to scale components independently and more cost effectively (Microservices!)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1813a81c-8b2a-4bc5-adf5-7f6c466a7e33/Untitled.png)
    
    - Easier to focus on security concerns.  Can secure areas of platform in more fine-grained way (than monolith)
    - Adopt technologies more easily (since each microservice can have different tech stack) and faster
    - Embrace uncertainty in digital.  Being able to compose capabilities in new ways for new formats (e.g. mobile, wearables)
- Disadvantages of microservices
    - many options
    - takes time to get there (invest in upfront tooling and learning)
    - testing is more complex
    - monitoring isn’t free
    - resiliency isn’t free (e.g. cascading failures)
    - more boxes to manage (need to automate managing and provisioning machines)
    - distributed systems are hard (e.g. consistency, CAP theory, transactional boundaries)
    

**Principles of Microservices**

- Things you need to do microservices well
- 12 factors from Heroku are a mix of principles and constraints, with some general good advice in building applications
- Example for some org:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ac7853ae-7d86-4d49-8c00-a7ae882e5e72/Untitled.png)

- Principle 1 - Modeled around Business Domain
    - 
- Principle 2 - Culture of Automation
- Principle 3 - Hide Implementation Details
- Principle 4 - Decentralize All the Things
- Principle 5 - Deploy Independently
- Principle 6 - Consumer First
- Principle 7 - Isolate Failure
- Principle 8 - Highly Observable
