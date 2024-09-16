# Clean Code 

Few famous quotes
> Programming is the art of telling another human what one wants the computer to do.    _“Donald Knuth”_

> Any fool can write the code that a computer can understand. Good programmers write code that humans can understand.   _“Martin Fowler”_

Clean coding practices don’t slow you down. You can be fast and clean in your development.

### Resources

[CODE COMPLETE 2: Steve McConnell](https://stevemcconnell.com/)

[Clean Code: Robert C. Martin](https://blog.cleancoder.com/)

[The Pragmatic Programmer](https://pragprog.com/)

### Three Clean Code Principles

1. Right tool for the job

2. High signal to noise ratio

3. Self documenting

### Right tool for the job

This is a comprehensive Regex to validate an email address. Can you imagine trying to read or modify this?

![](/clean-code/imgs/ComprehensiveRegex.png)

Watch for the Boundaries for picking the right tool. The boundaries between these technologies get people in trouble.

![](/clean-code/imgs/BoundriesOfTools.png)

![](/clean-code/imgs/IncorrectWayOfPuttingDynamicValue.png)

![](/clean-code/imgs/CorrectWayOfPuttingDynamicValue.png)

Benifit of using right tool for the language
-	Benefit of code coloring which make the code easier to read
-	Any typo in the keyword will be caught immediately
-	Saperation of concerns
-	In this example, the JS file will be cached so one less file will be downloaded from the server
-	The code will be reusable
-	This file (or JS code) can be minify & obfuscate

Avoid using one language to write another language/format via strings

For example:- Avoid using C# strings to create: 
- 	Javascript
- 	XML 
- 	HTML 
- 	JSON
- 	CSS

So, One language per file.

Every technology is Potential Evil, for example,

*LINQ-2-SQL* could have		"Massive queries, outer joins"

*SQL* could have			"Embedding display logic"

*C#* could have 			"Writing JS and HTML in strings"

### Maximize Signal to Noise Ratio
TED rule

T - Terse (code should not be excessively wordy)

E - Expressive (it’s clear what the code is trying to do)

D - Does one thing (clear responsibility)

Why is Signal to Noise Ratio Important?
-	Number of parameters per method
-	Number of methods in a class
-	Number of variables currently in scope

The Signal naturally decays overtime and noise naturally grows overtime, So we must refactor regularly as we write and maintain our code to avoid creating a huge mess that needs to be replaced rather than maintained.

#### DRY Principle: Don’t Repeat Yourself
-	Clean code honors DRY principle by stating each piece of knowledge or logic once and only once. 
-	Relational Database Schema normalize their database in an effort to assure each piece of data is defined in a single place.

Copy and Paste is often a sign of a design problem and can easily be eliminated by creating a reusable function or adding a parameter to an existing function to make it more flexible. 
-	It decreases signal to noise ratio
-	Increases the number of lines of code
-	Create a maintenance problem

> “Measuring Programming progress by lines of code is like measuring aircraft building progress by weight.” – Bill Gates

Much like an aircraft our goal is to stay light by minimizing the lines of code in our codebase.

#### Self-documenting Code
-	“Understanding the original programmer’s intent is the most difficult problem.” 
-	Self-Documenting code strives to do four core things
    -	Clear intent
    -	Layers of Abstractions, so that the problem domain can be considered and walked through at different levels of detail.
    -	Format for readability – better reading experience
    -	Favor code over comments will assure that the code is expressive as it can be without relying on comments.

### Summary

![](/clean-code/imgs/Summary1.png)

### Naming

#### Naming Classes
A well designed class should have a single responsibility, and its name should help reflect that.

Class Naming Guidelines
-	Good class names are Noun and NOT verbs.
-	The name should be as Specific as possible, Keep in mind that specific names leads to smaller and more cohesive classes. Cohesive class has methods that regularly interact with one another.
-	The name should help lead the design in the direction of Single Responsibility. 
Avoid generic suffixes that add no values, Product**Info**, Product**Details**, etc. An instance of a product is quite logical. An instance of ProductInfo, that’s confusing.


#### Naming Methods

![](/clean-code/imgs/MethodNamings.png)

You will notice that .NET Framework lifecycle methods are listed dirty as well. Like, On_Init(), Page_Load(), etc..

These methods	are, by definition, poorly named methods because all they do is tell you when they are called, not what sits inside. By reading these methods you know nothing about what’s going on. Now you obviously can’t go renaming them because that’s backed into the framework, but what you can do is only place methods calls inside. This gives the reader a quick high-level view of what you are trying to accomplish at that point.

#### Rubber Ducking
Verbalizing aids creativity
-	If you are having a hard time naming a method or a class, it often helps to verbalize.
-	Explain what the class does out loud. And if you don’t have a coworker that wants to listen, just keep a rubber duck in your desk. If there is no one around who cares to listen, explain it to the rubber duck. This sounds silly, but it really does help.

#### Avoid Side Effects 
-	A well structured named method shouldn’t surprise consumers with unexpected side effects.
    -   For example, 
        -   CheckPassword() shouldn’t log the user out.
        -   ValidateSubmission() shouldn’t save.
        -   GetUser() shouldn’t create their session.
        -   ChargeCreditCard() shouldn’t send emails.
-	Make sure your methods doesn’t lie to your readers, people have to read through every line and make sure that it does exactly what it says it does.
-	Make sure method names are comprehensive and tell the whole truth.

#### Naming Warning Signs
-	If you find yourself using words like “And”, “If”, “Or” within your method names, it a sure sign you are trying to do more than one thing. Example, “SaveAndEmail”, “LoadAndCopy”, “CalculateAndDisplay”, “ProcessOrDeny”, etc. 
-	If you name the method to describe everything inside and need to use these words, it’s a sign you need to create two methods, not one. 

#### Avoid Abbreviations
-	The problem with abbreviations is few words have a consistent and unambiguous abbreviations in the first place.
-	For example, RegUser, RegistUser, RegisUser for a RegisterUser method.

#### Naming Booleans
-	Well-named Booleans should sound like they are asking a true/false question.
-	Examples of some bad Boolean variables, 
    - 	`Open`, `start`, `status`, `login`
-	Some clean examples of Boolean variables, 
    -	`isOpen`, `done`, `isActive`, `loggedIn` 
-	`if (login) {}` vs `if (loggedIn) {}`

#### Strive for Symmetry
-	Sometimes we deal with opposites in our code, and when we do, it’s important that we select variable names that have symmetry. They should be clearly opposites. Some examples of poorly chosen pairs,
-	`On/disable`, `quick/slow`, `lock/open`, `slow/max`
Some clean examples, 
-	`On/Off`, `fast/slow`, `lock/unlock`, `min/max`

**Summary**

![](/clean-code/imgs/NamingSummary.png)

### Writing Conditionals that Convey Intent

#### Boolean Comparisons
Compare Boolean Implicitly,

`if (loggedIn == true)` // not recommended

`if (loggedIn)`         // better way of writing


#### Boolean Assignments
-	To Increase Signal to Noise ratio, Boolean should be assigned implicitly as well.

![](/clean-code/imgs/BooleanAssignment.png)

#### Prefer Positive Conditions
-	Be positive, Don’t be anti-negative. For example, its better to say “Keep Working” then saying “Don’t stop Working”.
-	The negative requests creates a lurch in your mind, so writing negative conditionals increases the cognitive load and reduces readability.

for example,
    `if (!isNotLoggedIn)`  // a sign of "programming by accident"

    `if (loggedIn)`

#### Ternaries are Beautiful

![](/clean-code/imgs/TernariesAreBeautiful.png)

-	Clean coder must also remember the term, YAGNI : You aren’t gonna need it. (Add complexity when needed)
-	You should not introduce more complexity in our code just because we think we might need it in future. Go ahead and add the additional complexity when its truly necessary.
-	And also, like any tool, the Ternary can be abused. You should avoid using multiple ternary operators in a single conditional because it hurts readability. Don’t chain them together, but in this sort of instance, it helps somebody read our code more quickly.


#### Be Strongly Typed

-	Be Strongly Typed, Not “Stringly” Typed

![](/clean-code/imgs/BeStronglyTyped.png)

#### Avoid Magic Numbers

-	Numbers by themselves, without context, don’t convey any meaning or intent.
-	Here is an example of a very simple conditional, the problem is we have no idea why 21 is significant. To figure that out we would have to read between the lines.
-	Magic numbers expect the reader to figure out what the number means.
-	Thus, Numbers are the wrong tool to do a job in conditionals. Instead consider setting a well known constant for use in conditionals.

```
// instead of 
if (age > 21)
{
    // body
}

// better way of writing
const int legalDrinkingAge = 21;

if (age > legalDrinkingAge)
{
    // body
}

```
-	Let’s see a different example for enums,

```
// instead of
if (status == 2)
{
    // body here
}

// better way of writing
if (status == Status.Active)
{
    // body here
}

```

#### Handling Complex Conditionals

![](/clean-code/imgs/HeadingComplexConditionals.png)

-	Using Intermediate Variables,

![](/clean-code/imgs/UsingIntermediateVariables.png)

-	Through the creation of this simple intermediate variable, we have clarified our intent.

![](/clean-code/imgs/EncapsulateComplexConditionals.png)

-	Further cleaned-up version using intermediate variables,

![](/clean-code/imgs/FurtherCleanedUpVersion.png)

These layers of abstraction really help the fellow programmers speed read through our code so that they can reach the line that they are looking for.

#### Prefer Polymorphism over Enums

![](/clean-code/imgs/FavorPolymorphismOverSwitch.png)

![](/clean-code/imgs/ClassesForDifferentTypeOfUsers.png)

With this structure, each class knows how to handle its unique behaviors. So no redundant switches are required.
-	Now, typically when you would use this approach, you would create a factory, and you would have a single spot where that switch statement determines which one of these user object would get instantiated.  But ultimately the details of how an active user logs in versus an inactive user versus a locked user would all be contained within these specific classes, which really helps you manage complexity with in these individual pieces.

#### Be Declarative

![](/clean-code/imgs/BeDeclarative.png)

-	Less likely to write a bug.

#### Table Driven Methods

-	Sometimes code isn’t the answer,

![](/clean-code/imgs/MultipleIfStatements.png)

-	This is called Hardcoding, because in this case the Insurance rates will regularly change. 
-	Avoid hardcoding values that change.
-	In these instances, the logic typically doesn’t belong in the code at all. Instead, it belongs in the database.

![](/clean-code/imgs/UseTable.png)

-	There are a variety of places that the table-drive approach makes sense,
    -	Insurance Rates
    -   Pricing Structures 
    -   Complex and dynamic business rules

**Summary**

![](/clean-code/imgs/CleanCodeConditionalsSummary.png)

### Writing Clean Methods 

#### When to Create a Function

#### Why create a Method – Reason 1: Avoid Duplication

#### Why create a Method – Reason 2: Excessive Indentation

#### Why create a Method – Reason 3: Convey Intent

#### Why create a Method – Reason 4: Do one thing

#### Mayfly Variables

#### How many Parameters

#### Sign a method is too Long

#### Handling Exceptions

### Writing Clean Classes

#### When to Create a Class (or extract a class from an existing class)

#### Class Cohesion Overview 

#### Names and Cohesion

#### Signs a Class is too small

#### The Proximity Principle

#### The Outline Rule

### Writing Clean Comments

#### Comments: A necessity and a Crunch

#### Dirty Comment 1: Redundant

#### Dirty Comments 2: Intent

#### Dirty Comments 3: Apology

#### Dirty Comments 4: Zombie Code

#### Dirty Comments 5: Divider

#### Dirty Comments 6: Brace Tracker

#### Dirty Comments 7: Bloated Header

#### Dirty Comments 8: Defect Log

#### Clean Comments

### Staying Clean

#### When to Refactor to Clean Code 

#### Code Reviews

#### Accept No Broken Windows
