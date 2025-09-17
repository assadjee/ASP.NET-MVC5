# Action Filters in ASP.NET MVC

## Introduction

Action filters in ASP.NET MVC are attributes that allow you to run code before or after an action method executes. They are useful for various cross-cutting concerns such as logging, authentication, authorization, and caching. This chapter will explore how to create and use action filters effectively.

## 1. What are Action Filters?

Action filters are a type of filter attribute that can be applied to controllers or action methods. They can execute logic before an action method runs or after it has completed. This is useful for tasks like:

- **Logging**: Track when an action is executed.
- **Authorization**: Check if a user has permission to access an action.
- **Caching**: Cache the result of an action to improve performance.

### Types of Action Filters

- **OnActionExecuting**: Executed before the action method runs.
- **OnActionExecuted**: Executed after the action method runs.
- **OnResultExecuting**: Executed before the result is processed.
- **OnResultExecuted**: Executed after the result is processed.

## 2. Creating a Custom Action Filter

You can create custom action filters by deriving from `ActionFilterAttribute` and overriding the appropriate methods.

### Example of a Custom Action Filter

```csharp
using System.Diagnostics;
using System.Web.Mvc;

public class LogActionFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        // Log action execution
        Debug.WriteLine($"Action executing: {filterContext.ActionDescriptor.ActionName}");
    }

    public override void OnActionExecuted(ActionExecutedContext filterContext)
    {
        // Log after action execution
        Debug.WriteLine($"Action executed: {filterContext.ActionDescriptor.ActionName}");
    }
}
```

## 3. Applying Action Filters

You can apply action filters at different levels:

### At the Action Method Level
```csharp
public class HomeController : Controller
{
    [LogActionFilter]
    public ActionResult Index()
    {
        return View();
    }
}
```

### At the Controller Level

```csharp
[LogActionFilter]
public class HomeController : Controller
{
    public ActionResult Index()
    {
        return View();
    }

    public ActionResult About()
    {
        return View();
    }
}
```

### Globally

You can register action filters globally in `FilterConfig.cs`.

```csharp
public class FilterConfig
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        filters.Add(new HandleErrorAttribute());
        filters.Add(new LogActionFilter()); // Global filter
    }
}
```


## 4. Summary

Action filters are a powerful feature of ASP.NET MVC that allows developers to add reusable logic to their applications. By creating custom filters, you can handle cross-cutting concerns efficiently and maintain cleaner controllers.

## Next Steps

In the next lecture we will explore about models and model binding in ASP.NET MVC, focusing on how to convert form data into .NET objects.