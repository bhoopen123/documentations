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
