## Azure Functions

### What are Azure Functions and when should we use them?

Azure functions are basically built on the idea of executing some code based on some events on azure cloud.
Azure Function = Events + Code
Events could be something as below, 
-	Timer :

	Run a background task on a regular schedule. For example: - clean-up some database or space, generate reports, etc.
-	Data :

	Process messages in a queue or new items in a blob storage. Perform some task when there is a new item in a queue, blob, etc.
-	Web :

	Respond to a HTTP request or web-hook.

Azure functions are also called as sever-less code, and are built on top of Azure WebJobs SDK.

We should use Azure Functions, mostly when there is a need to perform some task based on Event with Severless code.

### Benefits:-

-	Provides Simplified Programming Model
    -   Just the code to respond to the event.

-	Consumption based pricing model.

Azure functions, runs in an “App Service Plan”, so pricing plans of an “App Service Plan” is applicable for Azure Functions.
Or, we can also use “Consumption based” plan.

### Consumption Plan:-

Billing Model
-	Number of Executions
-	CPU Time (sec) * RAM (GB)

Free Monthly grant, 
-	1, 000, 000 executions
-	400,000 GB-s

5 mins execution time constraint. Its recommendation that execution time for Azure Function should not be more than 5 mins, Azure function will reset/restart if a task exceeds 5 mins.

### Alternative Pricing Options
Regular App service plan 
-	Pay for dedicated servers, which is good to predict monthly cost
-	Several pricing tiers
-	No function duration constraints

Premium Plans

Docker Containers

### Benefits of Azure Functions:-
-	Rapid and simple development, even code within Azure portal
-	Cost effective pricing
-	No server to maintain
-	Automatic scaling
-	Run your custom code on Azure Functions to respond to events

Cloud Functions are also being called Function as a Service (FaaS)

### Function App Parameters
When creating an Azure Functions, following parameters require, 
-	Name 
-	Subscription 
-	Resource Group
-	OS (Windows/Linux)
-	Hosting Plan (Consumption or App Service)
-	Runtime Stack (.NET, node.js, Java)
-	Storage Account
-	Application Insights

### Azure Functions Trigger Types:-
-	Timers, HTTP requests
-	Queue messages, blobs, CosmosDB

### Input and Output Bindings:- 
-	Connect to external resources
-	Reduce amount of code 

### Developing Azure Functions:- 
-	Visual Studio 2017 or newer
-	Azure Functions Core tools (Open Source)
    -	Cross-platform
    -	Command line
    -	Need DOTNET SDK

`npm install –g azure-functions-core-tools`

Main files in Azure Function project, 
-	host.json
-	local.settings.json
-	functionApp.csproj

### Route Templates 
-	`default is api/<functionName>` for example:- api/getCustomer?id=123

### Authorization Level
-	Anonymous 
-	Admin
-	Function

**AuthorizationLevel.Function:-** Which means a secret code will be required in order to call the function when it is deployed in Azure. Secret code is not required when running the code locally.

Use `Anonymous` AuthorizationLevel to make the function publically available.

### Database Bindings:- 
Output Bindings,
-	Azure Cosmos DB
-	Azure Table Storage

### Azure SQL Database:-
-	No built-in support yet (need to check now)
-	Use your own EF models

### Azure Function could have multiple output bindings:- 
For example: - Queue, Table (Azure), etc.

### IBinder Interface:-
 -	Customize any binding at runtime, for example,
```
var output = await binder.BindAsync<TextWriter>(new BlobAttribute($”licenses/{order.OrderId}.lic”)) {connection = “AzureWebJobsStorage”});
```
-	Works with all binding attributes (e.g. QueueAttribute, SendGridAttribute).
