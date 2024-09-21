## ASP.NET Core MVC 2.2 Application using Visual Studio

### What is ASP.NET Core?

![](/asp.net-core-mvc-2.2/imgs/RoadToAsp.netCore2.2.png)

-	ASP.NET MVC was based on same foundation as ASP.NET web forms.
-	.NET started aiming cross platforms support and light weight development. 
-	ASP.NET 2.1 & 2.2 were brought quite a new features e.g. SignalR and Razor class libraries.
-	ASP.NET core is built on top of .NET Core and therefore inherits its benefits, as cross platform support for example Linux and Mac and it is not tied to .NET framework.
-	MVC is a framework, built on an architectural pattern MVC (Model View Controller).
    -	Architectural Pattern
    -	Separation of concerns
    -	Promotes testability and maintainability 
-	Microsoft.AspNetCore.App:- is a base package, also known as a Meta package which references a lot of other packages.
-	appsettings.json:- is used to store settings of the applications.
-	wwwroot:- is basically a folder which is used to keep static files (javascript files, CSS files, and images files) of the application.
    -	If an image file is kept in wwwroot as wwwroot/images/image1.jpg, now this image can be accessed using below URL pattern.
    -	www.mywebsite.com/images/image1.jpg
-	Program.cs:- 

![](/asp.net-core-mvc-2.2/imgs/Program.cs.png)

An ASP.NET Core application is in fact a console application and therefore it contains a main function that will execute. In the main function, a host is being setup which will configure a server and a request processing pipeline. 
CreateWebHostBuilder which will setup the application with some defaults using WebHost.CreateDefaultBuilder.
WebHost.CreateDefaultBuilder:- does the setup, uses built- in web server called Kestrel. Kestrel is an internal web server. It’s not the web server which is going to Host the entire web application, for that we still need IIS.
By default, your ASP.NET Core application is hosted by his internal web server.
UseStartup<Startup> we are passing a type parameter ‘Startup’ which actually performs a startup or configuration of the application.
-	Startup.cs:- In the startup you will actually be doing two things 
    -	Defining the request pipeline 
    -	Configuring the all services that we need throughout the application
-	ConfigureService method: - registering services for our application. ASP.NET uses built in dependency injection system. Through dependency injection, we can achieve a loosely coupled architecture within our application. Instead of instantiating the class directly dependencies will be injected into the consuming classes. ASP.NET Core comes with built-in dependency injection container, a component that will be helping us to register and retrieve dependencies. This container is available to `IServiceCollection` interface and the Service collection is the collection of services it manages. Services are objects that have a certain functionality for other parts within the application. We will be able to register System Services as well as our own services.
    -	For example:- services.AddMvc(); in configureService method.

-	Configure method: - the request pipeline will be setup. The request pipeline consists out of a number of components chained behind one another. These are also called middleware components which intercepts of handles incoming HTTP requests. Each component will be able to alter the request, or response, or simply pass it on the next component in the request pipeline. A sample code, 

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseDeveloperExceptionPage();
    app.UseStatusCodePages();
    app.UseStaticFiles();
    app.UseMvcWithDefaultRoute();
}
```

Since, all of these will be in pipeline, the order in which they execute is important. Typically your call to the Mvc Middleware component needs to be at the end of the pipeline.

`app.UseMvcWithDefaultRoute()` should be placed after the `app.UseStaticFiles()`, 

`app.UseStaticFiles()` is the typically one which short circuit the request, and therefore basically not bother MVC for request for resources for example images. This will bring down the load on server because those requests can be handled by the static files middleware.
So the sequence in which you add the middleware is important here.

![](/asp.net-core-mvc-2.2/imgs/StartupOfTheApplication.png)

![](/asp.net-core-mvc-2.2/imgs/TheMVC-in-ASP.NET-Core-MVC.png)

#### The Model:- 
Domain Data + logic to manage data
The model should also have a simple API, the main goal is to hiding the nitty details that how we manage and interact with data with user or consumer.

For example, following class is a part of Model.

```
public class Pie
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string ShortDescription { get; set; }
    public string LongDescription { get; set; }
    public decimal Price { get; set; }
    public string ImageUrl { get; set; }
    public string ImageThumbnailUrl { get; set; }
    public bool IsPieOfTheWeek { get; set; }
}
```

#### The Repository:
The repository allows our code to use objects without knowing how they are persisted.

```
public interface IPieRepository
{
    IEnumerable<Pie> GetAllPies();
    Pie GetPieById(int pieId);
}
```

#### Registering the Repository:

![](/asp.net-core-mvc-2.2/imgs/RegisteringTheRepisitory.png)

We will register our repository with a dependency injection system, all other classes that we want to make use of repository can then ask the dependency injection for an instance of the repository. This way will create more loosely coupled code that was one of the goals of MVC.

**services.AddTransient:-**
For example: `services.AddTransient<IPieRepository, MockPieRepository>();`
If someone asks for an instance for IPieRepository, a new instance of MockPieRepository will be returned. It always returns a new instance of MockPieRepository class whenever IPieRepository instance is requested.

**services.AddSingleton:-** 
For example: `services.AddSingleton<IPieRepository, MockPieRepository>();`
As its name, only one instance is going to be created and return same instance whenever requested.

**services.AddScoped:-**
For example:- `services.AddScoped<IPieRepository, MockPieRepository>();`
One instance will be created and passed on to for a request cycle, and on the next request a new instance will be created and passed on.

#### Views:- 
**_Layout.cshtml:-** 
It contains the full page markup code with a placeholder (which is a razor expression) `@RenderBody()`- will be replaced by the view.
Now our Views need to point to the layout file they want to use, we can do that in each view directly as below, 
```
@{
Layout = “_Layout”
}
```
But typically we use so-called `_ViewStart.cshtml`. this is another cshtml file that will be searched for automatically by ASP.NET Core 2 when a view is being rendered. In a ViewStart file we typically place code we want to execute when just any View is being called. In that we will specify which Layout file should be used.

HomeController is the default controller that ASP.NET Core MVC knows and starts by default. 

**Question:** What if there is no Controller with name HomeController and run the application?

**Answer:-** Will get “Status Code: 404; Not Found” message on the page, if no other Controller is registered as Default controller in ASP.NET Core Routes.

**Client-Side-Library:-** to add client side libraries e.g. ‘bootstrap’ to the project.

**Entity Framework:-** is an ORM (Object Relational Mapper) from Microsoft, an ORM will allow us to work with Data in Database without having to write directly any SQL statements (in most cases) and basically allows us to work with the types that will behind the scenes be persisted. EF Core will allow us to work with LINQ Statements to interact with our Data. EF Core is lightweight and Cross-platform. 
EF Core only supports Code first approach. The Database first approach is not supported any longer.
-	`appsettings.json` file is read by ASP.NET Core runtime by default.


**Question:-** How to add multiple JSON settings file in .NET Core project?

**Answer:-** Microsoft.Extensions.Configuration provides a JsonConfigurationExtentions class which has multiple extension methods on IConfigurationBuilder class, method name is `AddJsonFile( )`.



