## What is Microservices ?
Microservices is an architectural style where autonomous, independently deployable services colleborate to form an application or system.

#### How small should they be?
There isn't an official size limit, some teams try to keep extremely small but many of the benefits of microservices still apply event if they are bit larger. 

#### The problem of monoliths
- Single codebase
- Single process 
- Single host 
- Single database
- Consistent technology (one programming language or SDK)

#### Monolith Benefits
- Simplicity
- One codebase
    - easy to find things
    - straight forward for developer to work on
- Deployment
    - easy deployment as only one application to update

So, there is nothing wrong using developing Monolith applications

#### Monolith Problems
- Problem of scale
- Difficult to deploy
    - Risky
    - Requires downtime
- Difficult to scale
    - horizontal scaling often not possible
    - vertical scaling is expensive
    - whole application must be scale

#### Distributed Monolith

![](/microservices/imgs/DistributedMonoliths.png)

- All services are tightly coupled together in such a way that everything must be deployed together.
- New features requires changes to multiple services and database.
- Such type of systems are referred as Distributed Monoliths.

### Benefits of Microservices

#### Small Services
- Can be owned by a team 
- Easier to understand
- Can be rewritten

#### Technology Choice
- Adopt new technology without need to upgrade everything in one go.
- Use the right tool, for example one Microservice uses relational database to store its data while another uses document database.
- Ofcourse, we need to standardize where it make sense, but individual them can decide which tool suite is more appropriate to use.

#### Individual Deployment
- Loose coupling means that we can deploy tem individually
- Which leads to much lower risk than upgrading everything in the entire system in one go
- Also helps us to achive minimizing downtime
- Frequent upgrades are possible

#### scaling
- Scale each Microservice individually, which is cost effective than scaling an entire Monoliths.

#### Agility
- Adapt rapidly changing business requirements
- Easier reuse

