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

#### Naming Methods

#### Rubber Ducking

#### Avoid Side Effects 

#### Naming Warning Signs

#### Avoid Abbreviations

#### Naming Booleans

#### Strive for Symmetry

### Writing Conditionals that Convey Intent

#### Boolean Comparisons

#### Boolean Assignments

#### Prefer Positive Conditions

#### Ternaries are Beautiful

#### Be Strongly Typed

#### Avoid Magic Numbers

#### Handling Complex Conditionals

#### Prefer Polymorphism over Enums

#### Be Declarative

#### Table Driven Methods

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
