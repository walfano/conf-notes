# OSCON 2019

July 15-18

Portland, OR

## Software Architecture Foundations

[Software Architecture Fundamentals](https://learning.oreilly.com/videos/software-architecture-fundamentals/9781491998991/)

### Architecture kata
- [full list](http://nealford.com/katas/list.html)
- Going Going Gone - online auction system
- Silicon Sandwiches - online ordering

always define implicit requirements
no clear binaries, everything is a trade-off
- always shoot for the least worst design

### Architecture Characteristics
- *influences some structural aspect of the design
- *specifies a non-domain design consideration
- *critical or important to application success
- (don’t call it “non-functional” characteristics)
- reliability, scalability, security, etc., etc., etc,
    - list of system quality attributes (wikipedia)
    - nail these down to objective definitions
    - often interact with each other (increasing security decreases performance)
- synergistic - part of the overall design
- one of the potential problems is *over* specifying - need to aggressively filter these characteristics to the ones that really matter
- understand problem domain
- feasibility, agility, elasticity, scalability

### Identify Driving Characteristics

- going going gone
    - requirements
        - “online auctions”
        - “auctions must be as real-time as possible”
        - “nationwide scale”
        - “participants must be tracked via a reputation index” (this is still TBD)
    - availability, reliability, performance, scalability, elasticity, (security)
        - scalability - measure of gradually increasing users
        - elasticity - ability to handle burst of users at once
    - prioritize the architecture characteristics
        - priority A - critical to application success
        - priority B - still important
        - priority C… - nice to have
        - de-prioritize security - offload credit card charges to 3rd party service
    - architecture characteristic vs part of the design?
        - does it require knowledge of the problem domain or can it be defined in the abstract?
    - designs need to be iterable

- silicon sandwiches
    - performance, availability, reliability, scalability, elasticity
    - customizability (internationalization, localization, global/local customization)
    - budget concerns - can potentially de-prioritize performance
        - could de-prioritize some other characteristic which would alter the design

### Architectural Governance

establish principles of your architecture that can be tested

### Evolutionary Architecture

- an evolutionary architecture supports guided, incremental change across multiple dimensions
- architectural fitness function - any mechanism that provides an objective integrity assessment of some architectural characteristics
    - can do automated testing around fitness functions
    - unit tests, monitors, metrics, chaos engineering, etc.
- avoid cyclic dependency function - hurts modularity
    - jdepend - tests that a package dependency cycle does not exist
- "maintainable"? - what does it really mean? (not objective or measurable)
    - cyclomatic complexity
    - naming conventions
    - immutability
    - limited inheritance - creates coupling
    - controlled afferent/efferent coupling

archunit.org (java)
- unit test java architecture
- enforce layer dependency

netarchtest (.NET)

performance
- cumulative average response times, maximum response times, cumulative average respoinse time
- chrome lighthouse - measure web page performance

### Component-based Thinking

- component identification
    - think about artifacts within the architecture in terms of components
    - building block of the application
    - well defined set of operations
    - subsystem, layer, service, or event processor
    - identify initial core components -> assign user stories to components -> analyze roles and responsibility statements -> analyze non-functional aspects -> refactor -> repeat
    - identify initial components using core functionality
        - partitioning comes naturally from the language of the problem domain
        - match your components as close as possible to how your problem is expressed
    - component granularity
        - break down large complicated components into more granular, well-defined componends
    - you’ll never get it right the first time
        - unknown unknowns drive iteration
        - iteration is critical
- going going gone
    - identify initial core components
        - the “entity trap” - “manager”/“workflow”/“utility” is an entity mapper
        - workflow approach (event storming)
            - create auction -> find auction -> sign up -> watch auction -> place bid
        - actor/action approach
            - bidder - view live video stream, view live bid stream, place a bid
            - auctioneer - enter live bids into system, receive online bid, mark item as sold
            - system - start auction, make payment, track bidder activity
            - components
                - auction session
                - bidder tracker (notified when auction starts)
                - video streamer
                - bid streamer
                - bid capture (talks to bid tracker and bid streamer)
                - auto payment (notified by auction session)
- silicon sandwiches
    - option A
        - location
        - product
        - purchase
        - promotion
        - delivery
    - option B
        - product
        - purchase
        - delivery
        - local
        - global

- architectural quantum
    - an independently deployable component with high functional cohesion, which includes all the synchronously coupled structural elements required for the system to function
    - architectural characteristics live at the quantum level

is this correct/the best? - “it depends…”

github scientist - run experiments at the architecture level

### Architecture Patterns

help define the basic characteristics and behavior of an application

- microservices architecture
    - microservices is a label, not a description
    - extreme levels of decoupling, isolated even at the data level (ideally)
    - driver is agility & incremental change
    - each microservice is meant to capture a bounded context
    - not meant to be prescriptive about size of application/# of lines of code
    - do not do transactions across microservices
    - consumer driven contracts - meant to enforce messaging in decoupled system
    - drivers
        - modularity
        - agility
        - fault-tolerance - redundancy, service discovery
        - scalability
        - testability
        - deployability
        - evolvability
    - not particularly performant, network latency
    - simplicity is poor due to the number of components
    - domain partitioned

- service-based architecture
    - many of the same things, just not as good as microservices
    - performance is a little better, simplicity is a little better, cost is a little less
    - domain partitioned

- layered architecture
    - presentation, business, services, persistence, database layers
    - drivers
        - simplicity
        - cost
    - it’s a great starter architecture, but it does not age well
    - not great for agility, deployment, scalability
    - technical partitioning - partitioning at the technical layer (persistence, database, etc.)
        - taken your domain and smeared it across multiple layers

- types of monoliths
    - unstructured monolith “big ball of mud”
    - layered monolith
    - modular monolith
        - top-level partitioning is around domains or workloads

- event-driven architecture
    - broker topology
        - initiating event -> event channel -> listener/processor -> event channel -> etc.
        - “choreography” message passing
    - mediator topology
        - adds in an event mediator as a central point through which all events pass through before going to event channels/processors
        - “orchestration” message passing
    - broker is more scalable since there is no bottleneck and less coupling
    - mediator is easier to deal with
    - drivers
        - modularity
        - agility
        - fault-tolerance
        - scalability
        - performance
        - elasticity
        - adaptability (preserve existing behavior and add to it)
        - evolvability (change behavior over time)
    - poor in testability and simplicity
    - technically partitioned

- pipeline architecture
    - aka pipes and filters architecture
    - producer | transformer | transformer | tester | consumer
    - drivers
        - modularity
        - simplicity
        - cost
        - agility
        - adaptability
        - evolvability
    - very simple architecture
    - technically partitioned

- microkernel architecture
    - core system that is relatively unchanging, plug-in components that allow you to drive certain behaviors
    - drivers
        - modularity
        - simplicity
        - cost
        - adaptability
        - evolvability
        - agility
        - testability
        - deployability
    - not great in scalability and performance
    - can be either technically and/or domain partitioned architecture

- space-based architecture
    - tuple-based, build tuples in memory that spin up individual processing units that eventually reconcile data
    - some have migrated to a giant distributed cache
    - drivers
        - scalability
        - elasticity
        - performance
    - very expensive and complex, but really good at performance and scalability
    - technically partitioned

- going going gone
    - hybrid microservices + event-driven architectures
    - implement each component as a service
    - use events
- silicon sandwiches
    - domain isomorphism - how well does the architecture map to the problem domain?
    - modular monolith using domain partitioning
    - microkernel
        - product, purchase, orders are commen in the core
        - can create custom plugins per location for global vs local

### Diagramming Software Architecture

- irrational artifact attachment - attachment to some artifact based on how long it took to produce it
- topology, protocols, etc.
- UML Distilled (if you need it)
- C4 model
    - system context, containers, components, classes

no correct answers in software architecture, only tradeoffs

### Architecture Decisions

- an architect is responsible for defining the architecture and design principles used to guide technology decisions
- too much documentation is just as bad as too little
- architecturally significant (Michael Nygard)
- ADR - architecture decision records
    - doc/arch/adr-NNN.md
    - short text file that is kept as a part of your codebase
    - title, status, context, decision, consequences
- asciidoc(tor)
    - asciidoctor.org

business needs and architectural characteristics are what drive the architecture
every architecture decision should be accompanied with a technical and business justification

### Resources

http://nealford.com/katas/
http://evolutionaryarchitecture.com 
- fitness functions katas
https://www.thoughtworks.com/podcasts
*** https://developertoarchitect.com 

