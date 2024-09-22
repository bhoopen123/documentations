## Understanding the Different .NET Platforms

### .NET Framework: -
-	Windows only
-	Currently 4.8
### Xamarin: - 
-	iOS and Android
-	Based on Mono
-	Mono is the original open-source version of .NET. 
### .NET Core
-	Cross-platform (windows, Linux, and Mac)

![](/dotnet6-basics/imgs/SharingCodeWithDotNETStandard.png)

-	.NET Standard has now been already for a few years and the way to share code. Meaning that we can share nearly all non-UI code using .NET Standard.
Now with .NET 6, Microsoft is taking things one step further, with .NET 6 the different platforms are now being unified. We don’t say .NET framework, .NET Core anymore. All there now is .NET 6 and going forward. It is the same .NET which will be used to build all types of applications like Desktop App, Cloud App, Web App, Games with Unity, Mobile App, and AI. 

![](/dotnet6-basics/imgs/OnePlatformToRuleThemAll.png)

### .NET 6 unification
-	Started with .NET 5 and completed with .NET 6.
-	Single SDK and BCL (Base Class Library)
    -	As a developer, just install the workloads you need.
-	Cross-platform native UI (Maui)
-	Cross-platform web UI (Blazor)

With .NET 6, one .NET vision has come to reality.
-	It started with .NET 5 in 2020.
-	It completed in 2021 with .NET 6. 

### Microsoft different Forms of support
#### “Current” release
-	Shorter lifespan support
-	Support only 6 months after release of next version
-	After that there will not be any security update anymore

#### LTS (Long-term support)
-	Supported 3 years after release of next version.

### Visual Studio 2022
-	Visual Studio 2022 is released with .NET 6.
-	Having new UI
-	64-bit process, all previous Visual Studio versions was using 32-bit process
-	**Hot reload** feature.
    -	Avoid app restarts.
    -	Code changes are visible in running app.
    -	Can be enabled on Save.
    -	Supported only in Debug configuration.
    -	Some code changes are not supported but in future it may also be supported.
    -	Hot reload works with, 
        -	ASP.NET Core
        -	Blazor (Server and WASM)
        -	Console
        -	WinForms, WPF and WinUI 3
        -	.NET Maui
        -	Azure Functions
    -	Some only work with .NET 6 Projects
    -	C# and VB supported
    -	Shortcut to use Hot Reload is “Alt + F10”
-	New Project Templates
    -	Less code in Program class
        -	C# 9 and 10 features
    -	 Web
        -	Bootstrap 5.1
        -	New Angular template
-	Refactoring and Intellicode Changes
    -	Backed by the cloud and provides code suggestions while writing the code
-	“Sync Namespaces” in the Solution
    -	Syncs the NameSpaces of each class based on the folder and project names
-	XAML live Preview

### What’s new in C# 10 

#### Global `using` statements
-	If a `namespace` is imported globally, its types are available everywhere in the project. You can declare a `using` statement to be global in every class or wherever you want. But the recommendation is placing these global using statements in a separate file, let’s say, `GlobalUsing.cs`.

#### File-scoped namespaces
-	Also named as File-based namespaces,  

![](/dotnet6-basics/imgs/FileScopedNamespaces.png)

-	Now we can see the snippet on the right “file-scoped namespaces”,  the semicolon at the end, now, that’s where the name comes from, all code in this file automatically becomes part of this namespace.

#### Extended property patterns
???

#### Structure type changes
???

#### Const interpolated strings
-	We can create Constant strings using interpolation, which before C# 10 was not possible.
 
```
const string folderPath = "/docs";
const string logFilePath = $"{folderPath}/log.txt";
```

-	The only constrain is that the placeholder variable should also be a constant.

#### “DateOnly” and “TimeOnly”
-	`DateOnly` – a struct that contains a Date without time
-	`TimeOnly` - a struct that contains a Time without date

#### Process Path and Process Id
-	ProcessPath  - returns the location of the executing assembly, so the path to the “.exe”.
    -	`Environment.ProcessPath`
-	ProcessId – returns the Id of the process.
    -	`Environment.ProcessId`

#### Chunk method in the LINQ API
-	For example,  `IEnumerable<type> values = values.Chunk(3)`

#### ThrowIfNull
```
void SomeVeryIntrestingMethod(object obj)
{
    ArgumentNullException.ThrowIfNull(obj);
}
```
 
Other differences
-	https://github.com/dotnet/core/tree/main/release-notes/6.0/preview/api-diff
 

### ASP.NET Core Platform

![](/dotnet6-basics/imgs/TheASP.NETCorePlatform.png)

-	With ASP.NET Core 6, the `Program.cs` and `Startup.cs` files are now unified into a single file (i.e. `Program.cs`) that replaces the functionality of both separate files that we had before.

#### Introducing Minimal APIs
-	To create small APIs with lot less ceremony than creating regular APIs with ASP.NET Core.
-	Part of .NET 6
-	Just few lines of Code
-	Example is shown below,
 
 ![](/dotnet6-basics/imgs/MinimalAPIsExample.png)

-	If we create an Empty API project in .NET 6, it creates one API which returns “Hello World”.
-	We can check this by implementing Swagger in our Empty Project.

#### Maui
-	`Xamarin.Forms` to `Maui`
-	Using Xamarin it was possible to create mobile apps for `iOS` and `Android`

Different versions of Xamarin
-	Xamarin.Forms: abstraction on top of “iOS”, “Android” and “Windows”.
-	Xamarin.Android and Xamarin.iOS 

With .NET 6, Xamarin.Forms, is getting successor named Maui. 
-	Maui -> Multi-platform App UI. Using .NET Maui and .NET 6, we can build native apps, both for mobile devices as well as Desktop.
    -	Maui can be used to build apps for Windows, iOS, Android and macOS.
    -	Cross platforms
    -	C# and XAML
  
![](/dotnet6-basics/imgs/.NetMauiArchitecture.png)

