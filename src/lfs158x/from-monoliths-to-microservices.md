# From monolith to microservices

## The legacy monolith

- The cloud is the new home for legacy applications, but not all legacy applications are ready to move to the cloud
    - Layers of features and redundant logic
    - Thousands of lines of code written in a *single*, but not-so-modern, programming language
    - Based on outdated software architecture patterns and principles
    - New features and improvements added to code complexity
    - Loading, compiling, and building times increase with every update
    - Despite all this overhead, the application runs on a single server (easy administration)
- These **monolith** applications required expensive hardware to keep it running
    - That single server it runs on must meet compute, memory, storage and networking requirements
    - Setups can be quite complex and costly, not to mention difficult to obtain at times
- The application runs as a **single process**
    - Scaling individual features is almost impossible
    - Scaling the entire application is doable with extra instances and pricey load balancing solutions
    - Upgrades, patches, migrations, and maintenance means system downtime is inevitable

## The modern microservice

- Carved out of a monolith to become distributed components. Put together, the make up the original monolith
    - Can be deployed individually on separate servers and provisioned with appropriate resources
    - Aligned with event-driven architecture and service-oriented architecture principles
    - Independent processes communicate through APIs and allow access to internal and external services
- Each microservice is developed and written in a modern programming language
    - Language is selected based on suitability for service type and business objective
    - Deployments can be tailored to the service to help reduce costs
    - Distributed nature of microservices adds complexity to application architecture but provides scalability
    - Modularity allows scaling to happen individually and can be configured manually or automatically
    - Updates are rolled out one service at a time, requiring virtually no downtime

### Refactoring

- As mentioned before, a legacy monolithic application may not be ready to be deployed as microservices
- Most migrations require refactoring to move to the cloud, either is one block or incrementally
    - Refactoring in one block may require stopping development of new features entirely
    - Refactoring incrementally has new features be developed as microservices while the monolith is slowly disassembled
- Refactoring monoliths into microservices gives them a second chance of life

### Challenges

- Refactoring a monolith into microservices is not always feasible, sometimes its better just to rebuild it from the bottom up
    - A monolith in a mainframe system written in older programming languages should be rebuilt
    - A poorly designed monolith should also be rebuilt and follow modern architectural patterns
    - Applications with tightly coupled data stores should also be rebuilt
- After refactoring, tools need to be put into place to ensure modules stay alive and resilient
    - Choosing runtimes can be challenging, especially if multiple modules are deployed to the same server
    - Differing libraries and dependecies can conflict with each other, leading to errors and failures
    - Forces deployment of single modules per server leads to poor resource management

### A solution

- Application container come along with the promise of consistent software environments from development to production
    - Encapsulate a lightweight runtime 
    - Ensure portability from physical bare-metal machines
    - Multiple applications can be deployed on the same server, this time with their own isolated execution environments
- Containerized applications lead to higher server utilization rates and provide individual module scalability and flexibility
- They can be easily integrated with automation tools
