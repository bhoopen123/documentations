## Logging Best Practices

### Logging
-	Functionality added to **software** so that someone (or something) can get insight into the **software**
-	Recording information about actions and states
-	Support guy doesn’t have access to the code, but they can look into the log files to get some idea about the problem occurred
-	You can’t debug the code in production, good logging is very helpful in such cases
-	Logging is like a second interface of your application
-	Auditing (knowing who did what), Profiling (which part of your application did what, in how many seconds), Statistics (like how many time this particular action was called)

### What should be logged
-	All errors and exceptions (including stack traces)  
-	Anything related to security and auditing (like, who did what)
-	Info about software version, startups and shutdowns (and generally all lifecycle)
-	Action taken by user and their results
-	Anything that may be used for debug or analyze

### What shouldn’t be logged
-	Sensitive data (service passwords, tokens, …)
-	User’s private data (passwords, auth tokens, financial data, …) 
-	Personal Identifiable Information (personal name, …)

### Log Levels 
-	Development Logs
    -	TRACE 
    -	DEBUG

-	General Logs
    -	INFO (like startups, shutdown, Users events, etc.)

-	Error Logs
    -	WARN (something which doesn’t impact the application is not working, e.g. Advertisement service not working, etc.)
    -	ERROR (some component is not loading, service is not working, etc.)
    -	FATAL (app crashed, etc.)
[Datetime] [Log-level] [message]

![](/logging-best-practices/imgs/LoggingExample1.png)

### Log Context 
Some of the things we can use as context are, 
-	Information about the user (User Id, Username, etc … ) we want to know which user did what action.
-	The current state
-	Parameters of the action
-	Results of previous Actions

Log should be Machine Parsable as well as Human Readable, 

### Concurrency in Logs 
-	We should associate each session with an ID? 

### Choose a library 
-	Don’t reinvent the wheel.
