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
-	Function vs Method 
    -	Both functions and methods are pieces of code, called by name.
    -	Core differences: Methods are associated with an object.
-	Functions organize code into small, targeted pieces of code. Functions are like paragraphs, for code.
-	4 reasons to create functions, 
    -	To avoid duplication
    -	Indentation: is a sign of complexity, and create functions to make the code more simpler to read.
    -	Unclear Intent
    -	Method should do one thing and one thing well,  but shouldn’t get very long.

#### Why create a Method – Reason 1: Avoid Duplication
Key is **Don’t Repeat Yourself**
-	Code is a liability – we would have to maintain the code. Duplicated code means two places to maintain, fix, and two redundant places to read.
-	To identify the duplicated code, look for patterns as well, Duplication in our code isn’t always obvious. We should look for the patterns to identify duplicates and use method parameters to implement it in a single method.

#### Why create a Method – Reason 2: Excessive Indentation
-	Excessive Indentation: Arrow Code

![](/clean-code/imgs/ArrowCode.png)

-	A sign of high cyclomatic complexity (i.e. A measure of the number of paths through the code)
-	High cyclomatic complexity, Hurts readability, Hinders Testing, and Increase bug risk.
-	There are three methods to eliminates the toxic arrow code, 
    -	**Extract Method:** We simply remove related piece of logic to a well-named methods that better convey intent and help us eliminate excessively deep indentation. When refactoring arrow code, it’s often simplest to start with the most deeply nested code and work your way out. 

![](/clean-code/imgs/ExtractMethod.png)

-	**Fail Fast:** Failing fast is really simple to comprehend. When there’s nothing more that you can do in a method, just fail. Now, failing in this case means that you throw an exception as soon as an unexpected situation that can’t be handed occurs.

![](/clean-code/imgs/FailFast.png)

Failing fast is also useful when working with switch statements, Every switch should have a default value so you are aware when the switch falls into an unexpected state.

![](/clean-code/imgs/SwitchStatementExample.png)

-	**Return Early:-** Returning early is another approach for eliminating deep indentation in our code. The big idea with returning early is if you have nothing more to do, return.
For example, look at below code sample, which is deeply indented.

![](/clean-code/imgs/ReturnEarlyInitial.png)

Can be written as below, there is no Indentation at all, also no need for the temporary `isValid` variable.

![](/clean-code/imgs/ReturnEarlyFinal.png)

> Use a return when it enhances readability. In certain routines, once you know the answer... not returning immediately means that you have to write more code. _Steve McConnell, "Code Complete"_

#### Why create a Method – Reason 3: Convey Intent
Let’s take an example we discussed earlier, 

```
// check for a valid extensions, confirm is admin or active

if ((fileExt == ".mp4" || fileExt == ".mpg" || fileExt == ".avi") && (isAdmin == 1 || isActiveFile))

```

We can completely clarify our intent in code by refactoring this conditional as below code, 

```

private bool ValidFileRequest(string fileExtension, bool isActiveFile, bool isAdmin)
{
    var validFileExtensions = new List<string>() {"mp4", "mpg", "avi"};

    bool validFileType = validFileExtensions.Contains(fileExtension);
    bool userIsAllowedToViewFile = isActiveFile || isAdmin;

    return validFileType && userIsAllowedToViewFile;
}

```

-	We have made clear that ‘mpg’, ‘mp4’ and ‘avi’ are the list of valid file extensions.
-	It is also clear that ‘isAdmin’ or ‘isActive’ is there to determine if the user should be allowed to view the file at all.
These layers of abstraction really help fellow programmers speed read through our code so that they can find the line they are looking for. 
The above method can be further refactored since it is doing two things, validating the requested file extensions and checking if the user has the rights to view the file.


#### Why create a Method – Reason 4: Do one thing

To keep the signal-to-noise ratio high, functions should do one thing. 
-	**Aids the reader: -** A well-chosen function name summarizes the functionality inside. 
-	**Promotes reuse: -** Because small, focused functions are easier to reuse than larger functions that do many things.
-	**Eases naming and testing: -** Functions that do one thing are much easier to name, and if a function is hard to name or has a name that’s too broad, that’s a sign that there’s an opportunity to split it up.
-	**Avoid side-effects: -** The reader should know what the function does by reading the name, If there is other potential surprising things going inside, consider refactoring to a separate function.

#### Mayfly Variables
-	The Mayfly has one of the shortest lifespans of any creature on earth. Many only live for 30 minutes, and the oldest only live for about 24 hours.
-	We should strive to give our variables mayfly-style lifetimes. There are two ways to do this, 
    -	Initialize variables just-in-time, when the variables needed, bring it to life. In the moment that it’s no longer necessary, get it out of scope so that the mental weight lifted.
    -	If you are creating targeted functions that do one thing, you are going to end up with mayfly variables automatically. 

#### How many Parameters
-	A high number of parameters makes s function harder to understand. It’s a sign the function is doing too much. 
-	Strive for 0-2 parameters, this will make code easier to read, understand and test.

![](/clean-code/imgs/InCorrectMethodParameters.png)

These parameters are called Flag Arguments, and it’s a strong sign that the function is doing 2 things. Flag Arguments should be avoided.

#### Sign a method is too Long
Few signs that the function may be getting too long, 
-	 Scrolling required: - If it doesn’t fit on the screen, it might be time to split it up. This is arbitrary but most of the functions should fit on the screen.
-	Naming issues: - A function should be easy to name if it has a single, clear defined task. So you have a hard time naming a function that totally describes what the function does, its likely too long.
-	Multiple conditions: - When multiple conditionals exits in a function, consider calling a separate function to extract out the conditional. This provides a clear description of the intent given the state of the conditional.
-	Hard to digest: - Methods that are hard to digest often work with more than one layer of abstraction at a time. A function should work with a single layer of abstraction. This makes it both easier to read and easier to debug.
-	A function is very hard to digest with seven parameters or more than seven variables in scope at a given time.
-	Simple function can be longer. Complex functions should be short.
Yes, this is arbitrary, but use your best judgement. 

#### Handling Exceptions
Exception Types
-	Unrecoverable: - Most common, examples, Null reference, File not found, Access denied
-	Recoverable: - When recoverable exception occur, all isn’t necessary lost. Its worth giving a second try. Examples, Retry Connection Try different file, Wait and try again. It’s important to consider ultimately giving up on retries at some point, so your application isn’t stuck in some infinite loop.
-	Ignorable: - Logging click, We were willing to accept not collecting some data in order to assure the user wasn’t our click logging system failed. There is nothing wrong with logging and discussion. You should never catch an exception you can’t handle intelligently. The correct behavior for a broken application is to crash immediately. The best way to make sure this happens is to throw an unchecked exception. 
-	Logging an error alone is not enough, 

![](/clean-code/imgs/TryCatchLogFailSlow.png)

![](/clean-code/imgs/TryCatchBodyStandalone.png)

![](/clean-code/imgs/WritingCleanMethodsSummary.png)


### Writing Clean Classes
-	Classes are like Book Headings

![](/clean-code/imgs/ClassesAreLikeBookHeadings.png)

-	A Heading in a book provides a high-level summary of the content inside. When the reader searches a specific concept later, these headings provide a road map. Heading make a book more scannable and help the reader comprehend the paragraphs inside more easily providing a high level summary of the topic being discussed. 
-	A well written heading should contain several related paragraphs underneath that stick closely to the topic outlined in the heading.
-	In the same way, a well named and targeted classes provide a strong queue to the high-level intent of what’s inside and thereby aid the reader.
-	A class should contain multiple methods that relate closely to a single clear responsibility.  


#### When to Create a Class (or extract a class from an existing class)
-	New Concept (Abstract or real-world)
    -	To model an object, these objects can model either concrete or abstract concepts.
    -	Classes are especially helpful with abstract concepts because they assign concrete name  and behaviors to these concepts and thereby make them more approachable. 
-	Low Cohesion (Methods should relate)
    -	If the methods in your class have little to do with one another, it’s a sign the class has low cohesion. Classes with low cohesion should be split up into separate classes with more targeted responsibilities.
-	Promote Reuse (Small, targeted => reuse)
    -	Even if a piece of code could be part of a larger class, put it in its own class if it is useful for another program and thereby easier to work with.
-	Reduce complexity (Solve once, hide away)
    -	 The class hides away the complexity and groups related operations behind the scenes, out of sight, out of mind, and allows somebody to just trust that the piece of code will work.
-	Clarify parameters (Identify a group of data)
    -	 Classes can greatly simplify information passing when complex data structures are involved. Classes can convert a loose bag of related variables into something more concrete, easier to reason about, and more tangible.

#### Class Cohesion Overview
-	Cohesion: How strongly the responsibilities of a class are related.
-	Class responsibilities should be strongly related.
-	This makes those classes, 
    -	Easier to read (Enhances readability)
    -	Increase likelihood of reuse – Highly cohesive classes are more likely to be reused because the logic inside is more likely to be discovered in the first place. 
    -	Avoids attracting the lazy – Class with low cohesion often have poor names as well, since its difficult to name a class with many disparate responsibilities. These classes are often given overly generic names that attract lazy developers who don’t want to think about where the logic truly belongs. It’s avoid attracting lazy developers, it’s important to keep class names descriptive. This helps keep cohesion high.
    -	To avoid creating low cohesion classes, keep an eye for a few things here… 
-	First, watch for methods that don’t interact with the rest of the class. Low cohesion classes feel more like a random set of functionalities.
-	In a similar way, watch out he fields that are only used by one method. Fields should be used by many methods in the class and if they are not, consider if the field or set of fields is a sign that the related functionality should be extracted to a separate class.
-	Classes that often change could potentially be a less cohesive class.

![](/clean-code/imgs/LowVsHighCohesion.png)

-	Imagine adding a new feature for financing a vehicle. It clearly belongs to “VehicleFinance” class.
-	It has also reduced the risk because the change to the “VehicleFinance” class should be highly unlikely to impact any functionality in the “VehicleMaintenance” or “Vehicle” class.

#### Names and Cohesion
-	Classes with overly general and undescriptive names lead to magnet classes, and as we know magnet attract items. In the same way, classes with poor names, attract lazy developers.
-	Classes with names like Common and Utility often grow quickly to thousands of lines of unrelated code.
-	The more concise the name, the smaller the class. So, start with naming well and strive for class cohesion.

#### Signs a Class is too small
-	Its rare to see the complain that the class is too small.
-	But its certainly possible to make the mistake that a class is too small, 
-	Few signs that a class is too small, 
    -	Inappropriate intimacy : If two classes rely heavily on one another and call a large portion of each other’s methods, then they might be a candidate for joining into a single class.
    -	Overly small classes make it difficult to understand how the system hangs together. There is just too many small moving parts. This can lead to issues like classes that are tightly bound to one another.
**Note:** these issues are very rare to see in the real world. Classes that are too big are more common.

![](/clean-code/imgs/PrimitiveObsession.png)

**Benefits:-**
-	Helps reader conceptualize the code an intent by assigning a well understood name to the group of parameters.
-	It takes something implicit (in this case a long list of Parameters), and makes it explicit by assigning the group and name that can be reasoned about.
-	Properties can be encapsulated if another property is added it doesn’t impact the method signature.
-	Aids maintenance: supports “find by reference”.

#### The Proximity Principle
Make code read top to bottom when possible. Keep related actions together.

![](/clean-code/imgs/ProximityPrincipleExample.png)

#### The Outline Rule
-	Collapsed code should read like an outline,

![](/clean-code/imgs/OutlineRuleExample1.png)

![](/clean-code/imgs/OutlineRuleExample2.png)

![](/clean-code/imgs/WritingCleanClassesSummary.png)

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
