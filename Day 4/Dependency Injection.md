# Dependency Injection in ASP.NET MVC 5 Using UnityContainer

## Step 1: Create a New ASP.NET MVC 5 Project

1. Open Visual Studio.
2. Create a new project and select "ASP.NET Web Application".
3. Choose "MVC" as the project template.

## Step 2: Install Unity Container

You can install the Unity library via NuGet Package Manager Console:

```bash
Install-Package Unity
Install-Package Unity.Mvc
```
## Step 3: Create Interfaces and Implementations
Create an interface and its implementation for a service. For this example, we will create a simple logging service.

Create the Interface
Create a new folder named Services, and inside it, create a new interface called ILogger.

```csharp

namespace YourNamespace.Services
{
    public interface ILogger
    {
        void Log(string message);
    }
}
```
Create the Implementation
Now create a class that implements this interface.

```csharp

namespace YourNamespace.Services
{
    public class FileLogger : ILogger
    {
        public void Log(string message)
        {
            // Logic to log message to a file
            System.IO.File.AppendAllText(HttpContext.Current.Server.MapPath("~/App_Data/log.txt"), message + "\n");
        }
    }
}
```
## Step 4: Create a Controller
Create a new controller that uses the ILogger service.

```csharp

using System.Web.Mvc;
using YourNamespace.Services;

namespace YourNamespace.Controllers
{
    public class HomeController : Controller
    {
        private readonly ILogger _logger;

        // Constructor Injection
        public HomeController(ILogger logger)
        {
            _logger = logger;
        }

        public ActionResult Index()
        {
            _logger.Log("Index action called.");
            return View();
        }
    }
}
```
## Step 5: Configure Unity
Now you need to set up Unity to resolve dependencies. Create a new class named UnityConfig in the App_Start folder.

```csharp

using System;
using System.Web.Mvc;
using Unity;
using Unity.Mvc5;
using YourNamespace.Services;

namespace YourNamespace.App_Start
{
    public static class UnityConfig
    {
        public static void RegisterComponents()
        {
            var container = new UnityContainer();

            // Register types
            container.RegisterType<ILogger, FileLogger>();

            // Set the DependencyResolver
            DependencyResolver.SetResolver(new UnityDependencyResolver(container));
        }
    }
}
```
## Step 6: Update the Global.asax
In the Global.asax.cs file, ensure that the Unity configuration is called on application start:

```csharp

protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    BundleConfig.RegisterBundles(BundleTable.Bundles);
    UnityConfig.RegisterComponents(); // Register Unity
}
```
### Step 7: Run the Application
Build and run the application.
Navigate to the home page.
Check the log.txt file in the App_Data directory to see if the log message has been recorded.
Conclusion
You've successfully implemented Dependency Injection in an ASP.NET MVC 5 application using the UnityContainer. This approach provides a simple and effective way to manage dependencies, promoting a loosely coupled architecture that is more maintainable and testable. You can extend this by adding more services and controllers as needed.