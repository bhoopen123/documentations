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

