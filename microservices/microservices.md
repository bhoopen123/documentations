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

### Challenges of Microservices

#### Developer Productivity
- How can you ensure developer's productivity?
- Do they have to download the entire code and configure each microservice to talk to each other?
- That could be very error prone and time consuming

#### Complex Interections
- Dividing an application in several Microservices will increase the complexity of the system
- Also becouse of chatty communication between microservices the entire application could be inefficient.

#### Deployment
- You will need to automate the deployment process
- Deployment of individual Microservice could be time consuming

#### Monitoring
- We need a certralized place to check logs and moniter for problems, if each Microservice has its own logs then checking logs for individual micriservices could be tedious.

#### Microservices give you choice
- Microservices donot dictate technology choice, database choices, synchronously vs asynchronously communication, what authentication mechanism you are going to use.
- Use your favorite programming language and tools.

#### Evolving towards Microservices
It is perfectly possible to evolve towards a Microservices architecture.
- Augment a Monolith with Microservices where everytime you add a new capability, you add a new Microservice.
- Decompose a Monolith into Microservices, where you identifies existing capabilities that should be extracted out into their own independent Microservices.

You don't need to start with Microservices, it might be better to allow a system to grow a bit until it becomes apparent what an appropriate segregation of responsibilities into Microservices would be.

- Defining Microservice responsibilities, how the public interface look like?

### Microservices own their own data
- Microservices are autonomous and independently deployable to achive this, Microservices has to own its own data.
- Avoid sharing data in Microservices
 
#### Microservice Data Ownership Limitations
- Database Joins
    
    By Splitting our data out, it means that we can no longer performs database joins across the data that's owned by two different microservices. Instead we have to make separate calls to each database.
    And we can no longer update two tables owned by two Microservices within a single database transaction.

- Transactions

    We have to use distributed transactions, which are very complex to implement, or more commonly design our system to work in what's called Eventually Consistency manner.

- Eventually Consistency

    Where we may have to wait a while for the overall state of the data to be fully consistent, when a single business operation requires updates to more than one data store, there is going to be a short window of time when the change has been made in one database but not the others.

#### Mitigating Data Ownership Limitations
- Define service boundries well

    Minimize the need to aggregate data from multiple microservices to perform a single operation.

- Caching

    One Microservice might hold its own cache of a subset of the data thatis owned by another Microservice. This also improves performance of the application and improves availability.

- Identify "seams" in the database schema

    This will help you avoid the performance penalties of having to involve multiple Microservices in a single business operation.

![](/microservices/imgs/SeamsInDatabaseSchema.drawio.png)

- Is Putting `Product Name` and `Unit Price` in OrderItem is not duplicating the data. Only putting `ProductId` should be fine? Well no, putting `ProductName` and `Price` are even correct to keep in OrderItem table. As it let us know the price of the item at the time of placing the order.

These data have different meaning within the context of each Microservices.

- Microservice can consist of more than one process Microservice Components

![](/microservices/imgs/MicroserviceProcesses.drawio.png)

There may be several different hosts and processes involved in a Microservice. But conceptually together they form a single Microservice and only those components are allowed to shared data.

#### Microservice should have clearly defined Public interface

#### Microservices can be upgraded without their clients needing to upgrade
This means that you must never make a breaking change to the public interface of a Microservice. Its a good idea to call a public interface a contract.

### Microservice Contract

#### Make additive changes
- Add new endpoints
- New properties on DTOs (Data Transfer Objects)

    As most of the serializers can be configured to simply ignore additional fields that they aren't expecting.

- Introducing version 2 API
    - Version 1 clients must still be supported

### Identifying Microservice Boundries
- What is the best way to break a system into Microservices?
- Getting it wrong can be costly.
    - Result in poor system performance
    - Difficult to change if service is running in production

So, it is worth dedicating some time considering, what our options are in this area.

In some way it could be easier if your system is existing Monolithic application. There might already have been modularization and some of these modules might be right candidate for converting into Microservices, especially if they are loosly coupled from the rest of the system through a clear interface.

#### Start from an existing system
- Identify loosely coupled components
- Identify database seams

    Organize Microservices around business capabilities.

#### Domain Driven Design
- Identify "Boundred Context" within our applications.
- Breaking up the domain model for large system
- each bounding context having its own ubiquitous language
- Microservices donot share models and even might use different terminology for the same thing, example, `OrderItem` and `CatalogItem`.

#### Sketch your ideas on a whiteboard
- Run them through real-world use cases
- Identify potential problems

### Microservice Boundry Pitfalls
- Don't turn every noun into a Microservice
    this lead to anemic CRUD Microservices, which is just a then wrappers around databases with methods add and updates entities, where the Logic related to those entities remains distributed across the rest of the system.
- Avoid circular dependencies between your Microservices.
- Avoid chatty communications between Microservices.

- eShopOnContainers Service Boundries

![](/microservices/imgs/eShopOnContainersServiceBoundries.drawio.png)

#### Microservice Hosting Environments
- Development

    Developers want to debug the aplication locally

- Staging

    Testers want to try out the application in a production like environment

- Production
    
    We also need to host the application for end users

### Microservices Hosting Options

#### Virtual Machines
- One VM per microservice, but that can get very expensive
- So, several Microservices onto a single VM, but that can get a bit messy, as each microservice might have different requirements for frameworks or dependencies that need to be installed. 
- There are several other operational challenges with using virtual machines, such as how each microservice can know where to find the others. 

#### Plateform as a services
- Automatic scale-out
- DNS address entries for each microservice
- Load balancing built-in 
- Security
- Monitoring
- Serverless plateforms also fall into this category

#### Containers
Containers let you package-up an application along with all its dependencies in such a way that its easily portable to run on any container host.
That container could also be run on the cloud, but it could also be your local development laptop.

### Creating a new Microservice
- Source control repository per microservice
    - Avoid tight coupling between services
- Setup continuous integration build
    - Run automated tests
