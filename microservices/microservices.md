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

### Types of Tests 

#### Unit Tests
These operates at the code level and they are typically very **fast to run**. If you are following a TDD process, then you will already be creating these and you typically want lots of Unit Tests and for them to have very good **Code coverage**.

#### Service- level Tests
Also called **Integration Tests**, we want to be also to test on individual microservices in **isolation** from all the others.

#### End-to-End Tests
These are where you test all of your microservices running to gether in a production like environment. You automate some key journeys through your application from the User Interface that excercise as many parts of the system working together as possible.

### Consider using a Microservice template Or "Exemplar"
It demostrate a pattern that other microservices should follow, it is a great idea to be have some kind of standard template that could be used as the starting point for every new Microservice you create, Or it could be that you nominate a particular microservice as an **exemplar**.

### Standardizing Microservices

It is a good idea to standardizing across all your Microservices, for examples, 

#### Logging
All of the microservices should be emitting logs in the same format and sending those logs to a centralized location. 

#### Health checks
Its a great idea for every Microservice to have a way of reporting its own health, like it is up and running, can it talk to its downstream dependencies such as databases or other Microservices. 

#### Configuration
All your Microservices should take same approach to accessing sequence and configuration. 

#### Authentication 
You might have some standard middleware that sets up the necesssary authentication pipeline for your technology of choice, such as ASP.NET, or Node.js. With that you will reduce the risk of developers accidentally misconfiguring authetication and leaving a microservice unsecured.

#### Build Script
It is a good idea for developers to build a 'Build Script' to build the code, for example, if you choose to use containers, so each Microservice needs a docker file that produces the container image.

### Benefits of Service Templates
- Reduce time to create a new microservices
- **Consistent tooling:** Ensures consistency in your product (but still allow for best tool for the job), don't be so rigid. The end goal of this is to provide a friction free development environment.
- **Increase developer productivity:** Developer's time should be focusing on implementing the business requirements of the microservice rather all the plumbing and infrastructure sorrounding microservices.
- Ability to run the microservice in isolation.

### Microservice communication Patterns
If we have few microservices, is it okay to call each other whenever its necesssary? and if I have a frontend application like a website or mobile application, should that also be allowed to call directly to any of the microservices it wants to?

![](/microservices/imgs/Microservices-CommunicationPatterns.png)

There will be few difficulties if we allow complete free-for-all situation like in the previous diagram for example, 
- Tangled dependencies between services
- Cascading failure may occur where one microservice failing causes the others to fail as well.
- Poor performanance, if making a call to one microservice ends up requiring multiple hops to call additional microservices.
- If such things are happenings in your aplication that means you have got some service boundries in the wrong place.

The better approach is to minimize calls between microservices,

![](/microservices/imgs/Microservices-CommunicationPatterns2.png)

- Microservices could share/publish the messages to a shared event bus and also subscribe to messages on the Bus. This promotes asynchronous communication between microservice.
- Frontend (mobile and/or single page application) calls go through the API gateway and are routed through to the correct microservice. API Gateway can have authentication implemented.
- A close related pattern called Backend for Frontend builds on this idea, you can create an API Gateway for each of the frontend application.

#### Synchronous Communication
- Making a direct call to a microservice and waiting for it to respond.
- It is important that this operation is optimized to complete as quickly as possible.
- HTTP is most popular mechanism, because it is an Industry standard, available in all main stream programming language.
Any web page or mobile application can easily call our Microservices.
- The payload of microservice request and response is typically going to be in JSON or XML.

#### RESTful APIs
Another pattern very often associated with microservice architectures is to implement your API as a set of RESTful resources.
- Represent information as "resources", e.g. CatalogItem, Order
- Each of the resources should be manipulated using HTTP methods, e.g. GET (to retrive), POST (to create), PUT (to update), DELETE (to delete)
- Use HTTP status codes, e.g. 404 (NOT FOUND), 200 (OK), 201 (CREATED)
- Media type headers (content type) to specify how our resources should be represented

#### Asynchronous Communication
Sometimes, we might want to ask a microservice to do something that we don't need to wait for e.g. process of submitting an order

![](/microservices/imgs/AsynchronousCommunication.drawio.png)

We can achieve this by making asynchronous communication patterns.

#### Asynchronous Communication over HTTP 
It is possible to implement asynchronous communication patterns over HTTP, for example, 
![](/microservices/imgs/AsynchronousCommunicationOverHttp.drawio.png)

Where the microservice itself reports back when its completed when its completed the task. When a client calls your microservice, they can register a callback URL on which they would like to receive any notifications. But anothervery common pattern for asynchronous comminications is for microservices to publish messages to an Eventbus. Rather than directly calling the other microservices, they simply create a message and send it to a message broker which services as an intermediatory, Other microservices than subscribe to those messages.

**Advantages:** 
- It completely decouples the microservices from each other, So instead of one service directly calling the next service, it simply talks to the message broker.
- If second service is temporarily unavailable, then the first service is still able to function and the second service will process the queued-up messages once its online again.
- This approach is also very beneficial for supporting more advanced scaling patterns. Many serverless hosting platforms do this automatically for you, but with containerized solutions, its also possible to achievethe same for the cluster of virtual machines that your containers are running on.

### Message Types

#### Commands
A command message is a request for a particular action to be performed. e.g. an email send microservice which may work asynchronously.
We could post a command message that specified the details of the email that should be sent and email microservice could pick that up and send the message.

#### Events
An event message is simply a way of announcing that something has happened and are not directed to any microservice in particular when you publish events you are allowing any other microservices in your system that are interested to subscribe to that event and perform their own custom actions when it occurs, for example,  if there was an "Order Placed" event then several actions might need to occur as a result of that event, like charging your credit card, sending a confirmation email, checking on stock levels. So each microservices that needs to perform an action when an orer is placed will be triggered by that single event being posted to the event bus.

### Resilient Comminication Patterns
We may expect from time to time that communication from servie will fail due to transient issues. We need to handle these failures well as they result in cascading failures that are very difficult to recover from.
Its a great idea to build in retries with back-off, this means if your first attempt fails, then you just wait a few seconds and try again, and if that fails the you wait a bit longer and make another attempt. And you might find that your programming framework of choise has got built-in support for this functionality, e.g. Polly in .NET

#### Circuit Breaker
A circuit breaker sits in between the client and the server and initially it allows all calls through. It is being called circuit breaker is "Closed".

![](/microservices/imgs/CirtuitBreaker1.drawio.png)

Pass call through (Circuit breaker is "closed").

However, if it detects any error, maybe the server is returning error codes, or not responding at all, then cirtuit breaker opens, which means now, whenever the client makes a call, its going to fail fast rather after a configured timeout is elapsed the circuit-breaker allowes some calls through to see if the service hasn't recovered then it remains in the open state a bit longer, again quickly rejecting any incoming requests.

![](/microservices/imgs/CirtuitBreaker2.drawio.png)

Many programming framework will have ready-made implementation of a circuit breaker that you can take advantage of.

#### Caching
Caching can also improve resilience. Fallback to cache data if the downstream service is temporarily unavailable. Of course, that assumes that you have thought about the implecation of using stale data. 

**Message-Broker** like Azure service bus, 
One of reasons message-brokers are so popular in microservice archirecture is that they have inherent support for resilience.
- Message can be posted if the recipient is offline
    - it can catch up later
- Message brokers support re-deliveries
    - put the message in Dead-letter queue after multiple failures.
- Messages may be recieved out of order
    - You need to make sure that message handler perform the right step, even if they recieve messages out of order.
- Same message may be recieved multiple times
    - The message handlers should be **Idempotent**. That means, if calling it twice with the same message has essentially the same effect as calling it once. e.g. Handling an order, we only want to charge you credit card once, and we only want to the order once.

### Service Discovery
For our microservices to be able to communicate with each other, each microservice needs to have an address. but how can we find out what address all the other microservices are located at?
We don't want give the Hardcore IP address to the VMs hosting microservices, as that gives us poor flexibility to dynamically move our.

![](/microservices/imgs/ServiceDiscovery.drawio.png)

Our microservices between virtual machines in the cluster, which is necessary in the cloud as machines do need to be taken down from time to time for servicing purposes.
One way of solving this problem by using **Service Registery**.'

This is a central service that knows where all the other microservices are located. Sometimes this works by each service reporting its location to the Service Registry when it starts up and keeping in touch still available at that address.

Whenever you want to communicate to another service, you can ask the service registry to tell you where that microservice can be found. And Service Registry itself is typically distributed across all the machines in your cluster, which makes it simple for you to be able to contact the service registry.

A number of microservice hosting platforms solve this tricky problem by means of DNS. e.g. If your using PaaS to host your microservices then each microservice you deploy will automatically be allocated a DNS name, that points to a load balancer sitting in front of the actual instances of your microservice. That means you can communicate to your microservices through DNS and not worry about the actual IP addresses of the individual Virtual Machine that are running the microservice and might change over time.

Container Orchestration e.g. Kubernetes also got its own in build DNS and handles routing for you.

### Securing Microservices 

#### Sensitive Data
e.g. Ordering Service
- Customer's Order History
- Payment information

There is a need to secue any Microservice that deals with sensitive data. Encryption plays an important role in protecting our data.

#### Encryption Data
- **Encryption in transit** means data transfers across the network should be encrypted.
- Use updated standard encryption algorithms.
- For HTTP communications, encryption can be achieved by using Transport Layer Security (TLS), so that all requests are made using HTTPs,
    - by configuring SSL certificates
    - many cloud Microservice hosting platforms provide mechanisms that simplifies the process of configuring HTTPS for your services.
- **Encryption at rest** means that whenever its (data) stored on disk, those disks should be encrypted.
    - **Disk encryption:** Cloud providers are offering encryption at rest at a standard feature for file storage and databases.
    - **Encrypt Backups:** The backups of sensitive data should also be encrypted.

#### Authentication
We need to know who is calling our service, Each incoming request need to tell us who the caller is. so the server can make a decision whether they are authorized or not.

Some options to achieve authorization, 
1. HTTP Authorization options
    - Use authorization header contain a username and password, its also known as "basic authentication".
    - With basic authentication approach, server has to store the password which (using) password hashing mechanism.

2. API Key
    - When one microservice calls through another microservice, the authorization header might contains an API Key.
    - You give each valid client of your microservice their own API key.
    - You need to manage more secrets (API Keys) and potentially rotate, if they get compromised.

3. Client Certificate
    - By using Public-Key-Cryptography, a certificate gives us a very secure way to caller to prove their identity.
    - Certificates can be complex to install and manage.

#### Using an Identity Server
Using Identity Server is an promissing opproach for authentication. It uses industry standard protocols, such as **OAuth2.0** and **OpenId Connect**.

![](/microservices/imgs/IdentityServer.drawio.png)

- Only one server (Identity Server) has the responsibility for authenticating users and managing their credentials securely.
- Because the Identity Server is implementing the industry standards protocols, like OpenId connect, so we don't need to write any code for this component. For example, **Identity Server 4** for an OpenId Connect and OAuth2.0 framework for ASP.NET Core.

#### Authorization
- Authentication: Who is calling?
- Authorization: What can they do?

Many web API frameworks, like ASP.NET Core provide built-in mechanism that help you perform authorization by checking various things about the user, such as, whether they have got a perticular role or not.

Consider carefully what callers should be allowed to do.

#### Confused Deputy

![](/microservices/imgs/ConfusedDeputy1.drawio.png)

When the Ordering Microservice calls the Payment Service, it might send Credentials that simply say, that this is a ordering service making the call. And because the Payment Service trusts the Ordering Service, it assumes that its safe to perform whatever action has been requested. But this could be risky because what if I could somehow trick the ordering service to ask the payment service to pay my order but using someone else's credit card details. This issue is known as **Confused Deploy**.

One solution of this problem is by using **On behalf of** access tokens. This way when Ordering Service calls the Payment Service, it still identifies itselfs as the Ordering Service, but it also tells the Payment Service that this is a call being made on behalf of that user. And that gives Payment Service more information to go on and it can reject the request the user is paying with someone else's card.

![](/microservices/imgs/ConfusedDeputy2.drawio.png)

#### Securing the Network
Another important defense is to use the network features such as firewalls, IP Whitelisting and virtual networks to secure your microservices.

![](/microservices/imgs/SecuringTheNetwork.drawio.png)

- Because of Virtual Network, we have the ability to reject incomming traffic from outside the virtual network and only them to communicate within each other.
- This idea protects back-end microservices from external access.
- If any application do need to communicate to one or more of these microservices, then **API Gateway** or **backend for frontend** can be used.
- An API Gateway can be connected to internet as well as virtual network to pass on the incomming requests to the backend microservices.
- We can choose which backend APIs are exposed through the API gateway.
- API Gateway is also the single point of entry to the exposed backend microservices.
- Some cloud API Gateways can also be configured with a firewall and other types of attack protection, such as DDoS (Distribute denial-of-Service), SQL injection attacks.
- Some non-public facing websites like Marketing campain website or Administrative websites which are intended to use by company staff can be configured for **IP Whitelisting**, so this website rejects any traffic that doesn't come from certain known IP Address.

![](/microservices/imgs/SecuringTheNetwork2.drawio.png)

#### Defence in Depth
We have discussed several techniques to secure our microservices, 
- Encryption in transit
- Access tokens
- Network Securities
    - Vritual Network
    - Firewall
    - IP Whitelisting

- Defence in Depth principle states that you should not rely entirely on a Single technique to secure your application because if that one defense is breached then everything is lost.
- Apply multiple layers of protection for more sensitive data
- The above three techniques should be considered to be essential for securing any microservices application that deals with sensitive data.

#### Additional Defensive Measures

