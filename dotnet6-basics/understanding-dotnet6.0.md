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

