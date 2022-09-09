## ASP.NET


***

## Content

- [Installation](#installation)
- [CORS](#cors)
- [Middleware](#middleware)
- [Dependency Injection](#dependency-injection)
- [Page life cycle](#page-life-cycle)
- [Generic Host](#generic-host)
- [Static](#static)
- [ActionResult](#actionresult)
- [OAuth](#oauth)

***

# Installation

> MAC OS fixing ERRORS
>  - After Installation of .NET excute: 
>  - to Link directory `sudo ln -s /usr/local/share/dotnet/x64/dotnet /usr/local/bin/`
>  - Check `dotnet --version` or `dotnet --info`
>  - Https certificate issue:
>    - `dotnet tool install --global dotnet-dev-certs`
>    - `dotnet dev-certs https --trust` 


- API: Application Programming Interface
- REST Representational State Transfer: 
  - It is an standard
  - HTTP Verbs: - GET - POST - PUT - PATCH(Partial Updated) - DELETE
  - Only one URL per action
  - Response HTTP
    -  100 - 199
    -  200 - 299
    -  300 - 399
    -  400 - 499
    -  500 - 599


- creating Web API -  `dotnet new webapi` 
- creating minimal API -  `dotnet new web` 

[Back](#content)


# CORS

- CORS (Cross-origin resource sharing) is a technique that allows restricted resources on a web page to be requested from another domain outside the domain of which the first resource was served. 
- A web page may freely attach cross-origin images, scripts, stylesheets, iframes, and videos. 
- The same-origin security policy by default does not allow certain “cross-domain” requests, notably Ajax requests.
- Cross-origin resource sharing provides a way by which a browser and server can interact to determine whether allowing the cross-origin requests are safe or not.


[Back](#content)

# Middleware 

- It is actually sequential series of delegates (piece of code), that can either short-circuit or pass on the HTTP request to next delegate. 
- These are known as middleware, a concept well known to people who worked with Node.js.
- Piece of your middleware can do one of the following:
    - Handle an incoming HTTP request by generating an HTTP response (maybe your authentication or authorization middleware will stop the request early and immediately create response)
    - Process the incoming request, change it and pass it to the next middleware in the pipeline
    - Process the outgoing response, change it and pass it on to next middleware in the pipeline or directly to the `ASP.NET` Core web server
- Each middleware adds or modifies http request and optionally passes control to the next middleware component.

- **Configure Middleware**
  - We can configure middleware in the Configure method of the **Startup** class using `IApplicationBuilder` instance. 
  - The following example adds a single middleware using Run method which returns a string "Hello World!" on each request.
  ```cs
  public class Startup
    {
        public Startup()
        {
        } 
        public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
            //configure middleware using IApplicationBuilder here..
                
            app.Run(async (context) =>
            {              
                await context.Response.WriteAsync("Hello World!");
                
            });

            // other code removed for clarity.. 
        }
    }
  ```

- The followings are some built-in middleware:

| Middleware | Description |
| ---------- | ----------- |
|Authentication |	Adds authentication support. |
|CORS |	Configures Cross-Origin Resource Sharing. |
|Routing |	Adds routing capabilities for MVC or web form |
|Session |	Adds support for user session. |
|StaticFiles |	Adds support for serving static files and directory browsing. |
|Diagnostics |	Adds support for reporting and handling exceptions and errors. |



> Costum Middlewares shoul be implement after `app.UseAuthorization()` in Program.cs file .NET V 6.0
```cs
    // ...
    app.UseHttpsRedirection();

    app.UseAuthorization();

    // --Costum Middlewares--

    app.UseWelcomePage();

    // -------------------

    app.MapControllers();

    app.Run();

```



[Back](#content)

# Dependency Injection

Dependency Injection (DI) is a design pattern which implements the IoC principle to invert the creation of dependent objects.

> Inversion of Control (IoC): IoC Container (a.k.a. DI Container) is a framework for implementing automatic dependency injection.
> - All the containers must provide easy support for the following DI lifecycle.
>   - Register
>   - Resolve
>   - Dispose

- Built-in IoC Container
  - The built-in container is represented by `IServiceProvider` implementation that supports constructor injection by default. 
  - The types (classes) managed by built-in IoC container are called **services**.
  
- There are basically two types of services in ASP.NET Core:
    1. **Framework Services**: Services which are a part of ASP.NET Core framework such as IApplicationBuilder, IHostingEnvironment, ILoggerFactory etc.
    2. **Application Services**: The services (custom types or classes) which you as a programmer create for your application.

- Registering Application Service
  - Consider the following example of simple ILog interface and its implementation class. 
  - We will see how to register it with built-in IoC container and use it in our application.
  ```cs
  public interface ILog
    {
        void info(string str);
    }

    class MyConsoleLogger : ILog
    {
        public void info(string str)
        {
            Console.WriteLine(str);
        }
    }
  ```
- ASP.NET Core allows us to **register** our application services with IoC container, in the `ConfigureServices` method of the Startup class. 
- The `ConfigureServices` method includes a parameter of `IServiceCollection` type which is used to **register** application services.
- Let's register above ILog with IoC container in ConfigureServices() method as shown below.

  ```cs
  public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.Add(new ServiceDescriptor(typeof(ILog), new MyConsoleLogger()));        
        }

        // other code removed for clarity.. 
    }
  ```
- As you can see above, Add() method of `IServiceCollection` instance is used to register a service with an IoC container. 
- The `ServiceDescriptor` is used to specify a service type and its instance. 
- We have specified ILog as service type and MyConsoleLogger as its instance. 
- This will register ILog service as a singleton by default.

- **Understanding Service Lifetime:**
  - Built-in IoC container manages the lifetime of a registered service type. 
  - It automatically disposes a service instance based on the specified lifetime.
  - The built-in IoC container supports three kinds of lifetimes:
    - **Singleton**: IoC container will create and share a single instance of a service throughout the application's lifetime.
    - **Transient**: The IoC container will create a new instance of the specified service type every time you ask for it.
    - **Scoped**: IoC container will create an instance of the specified service type once per request and will be shared in a single request.
    ```cs
    // The following example shows how to register a service with different lifetimes.
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add(new ServiceDescriptor(typeof(ILog), new MyConsoleLogger()));    // singleton
        
        services.Add(new ServiceDescriptor(typeof(ILog), typeof(MyConsoleLogger), ServiceLifetime.Transient)); // Transient
        
        services.Add(new ServiceDescriptor(typeof(ILog), typeof(MyConsoleLogger), ServiceLifetime.Scoped));    // Scoped
    }
    ///////////////
    // Extension Methods for Registration
    // ASP.NET Core framework includes extension methods for each types of lifetime; 
    // AddSingleton(), AddTransient() and AddScoped() methods 
    // for singleton, transient and scoped lifetime respectively.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ILog, MyConsoleLogger>();
        services.AddSingleton(typeof(ILog), typeof(MyConsoleLogger));

        services.AddTransient<ILog, MyConsoleLogger>();
        services.AddTransient(typeof(ILog), typeof(MyConsoleLogger));

        services.AddScoped<ILog, MyConsoleLogger>();
        services.AddScoped(typeof(ILog), typeof(MyConsoleLogger));
    }
    ```

- **Constructor Injection**
  - Once we register a service, the IoC container automatically performs constructor injection if a service type is included as a parameter in a constructor.
  - For example, we can use ILog service type in any MVC controller. Consider the following example.
  ```cs
    public class HomeController : Controller
    {
        ILog _log;

        public HomeController(ILog log) // constructor injection
        {
            _log = log;
        }
        public IActionResult Index()
        {
            _log.info("Executing /home/index"); // class MyConsoleLogger - method

            return View();
        }
    }
    // IoC container will automatically pass an instance of MyConsoleLogger to the constructor of HomeController.
  ```

- **Action Method Injection**
  - Sometimes we may only need dependency service type in a single action **method**. 
  - For this, use `[FromServices]` attribute with the service type parameter in the **method**.
  ```cs
  using Microsoft.AspNetCore.Mvc;

    public class HomeController : Controller
    {
        public HomeController() // constructor
        {
        }

        public IActionResult Index([FromServices] ILog log) // method
        {
            log.info("Index method executing");

            return View();
        }
    }
  ```
> Property Injection: Built-in IoC container does **not** support property injection. You will have to use third party IoC container.

- **Get Services Manually**
  - It is not required to include dependency services in the constructor. 
  - We can access dependent services configured with built-in IoC container manually using `RequestServices` property of `HttpContext` as shown below.
  ```cs
  public class HomeController : Controller
    {
        public HomeController() // constructor
        {
        }
        public IActionResult Index() // method
        {
            var services = this.HttpContext.RequestServices; // manually Injection
            var log = (ILog)services.GetService(typeof(ILog));
                
            log.info("Index method executing");
        
            return View();
        }
    }
  ```
  > It is recommended to use constructor injection instead of getting it using RequestServices.



[Back](#content)

# Page life cycle  
- the events in `ASP.NET` page life cycle
1) Page_PreInit 
2) Page_Init
3) Page_InitComplete
4) Page_PreLoad
5) Page_Load
6) Page_LoadComplete
7) Page_PreRender
8) Render



[Back](#content)

# Generic Host

- The .NET Generic Host is a feature which sets up some convenient patterns for an application including those for dependency injection (DI), logging, and configuration. 
- It was originally named Web Host and intended for Web scenarios like ASP.NET Core applications but has since been generalized (hence the rename to Generic Host) to support other scenarios, such as Windows services, Linux daemon services, or even a console app.
- A host is an object that encapsulates an app’s resources, such as:
    - Dependency injection (DI)
    - Logging
    - Configuration
    - IHostedService implementations
- The generic host introduced with `ASP.NET` Core 3.0 and .NET Core 3.0 basically replaces the previous IWebHost and IWebHostBuilder. 
- It follows the very same architecture and idea but is simply reduced to non-web tasks so that it can work with a number of different purposes. 
- `ASP.NET` Core then just builds on top of this generic host.




[Back](#content)

# Static

- Your static classes and static instance fields are shared between all requests to the application, and has the same lifetime as the application domain.
- Therefore, you should be careful when using static instances, since you might have synchronization issues and the like. 
- Also bear in mind, that static instances will not be GC'ed before the application pool is recycled, and therefore everything that is referenced by the static instance, will not be GC'ed. 
- This can lead to memory usage problems.


[Back](#content)


# ActionResult

- ActionResult is used to represent the action method result. 
- Below are the subtypes of ActionResult:
    - ViewResult
    - PartialViewResult
    - RedirectToRouteResult
    - RedirectResult
    - JavascriptResult
    - JSONResult
    - FileResult
    - HTTPStatusCodeResult


[Back](#content)

# OAuth 
- OAuth (Open Authorization) is an open standard for access granting/deligation protocol. 
- It used as a way for Internet users to grant websites or applications access to their information on other websites but without giving them the passwords. 
- It does not deal with authentication.
- Basically there are three parties involved:
  - oAuth Provider, oAuth Client and Owner.
- oAuth Client (Application Which wants to access your credential)
- oAuth Provider (eg. facebook, twitter...)
- Owner (the person with facebook,twitter.. account )


[Back](#content)