# Show order in the Navigation bar:
## Step 1: Add `ServiceLocator` class in Global.asax.cs

```csharp
 public static class ServiceLocator
 {
     public static T Get<T>()
     {
         return (T)DependencyResolver.Current.GetService(typeof(T));
     }
 }
```
## Step 2: Update Layout View to Show "Orders" Menu for Admin
In shared layout file (_Layout.cshtml), check if the logged-in user is an admin and conditionally display the "Orders" menu item.

- Inject UserManager and RoleManager
Add the following at the top of your layout file:
csharp

```csharp
@using Microsoft.AspNet.Identity;
@using SportsStore.Models;
@using Microsoft.AspNet.Identity.Owin;
@{

    var userManager = ServiceLocator.Get<UserManager<ApplicationUser>>();
    var roleManager = ServiceLocator.Get<RoleManager<Microsoft.AspNet.Identity.EntityFramework.IdentityRole>>();
}
```
## Check User Role
Check if the user is in the "Admin" role:

```csharp
@{
    var isAdmin = false;
    if(userManager != null)
    {
       isAdmin = User.Identity.IsAuthenticated && userManager.IsInRole(User.Identity.GetUserId(), RoleName.CanManageProducts);
    }
}
```

### Update Navigation Menu
#### Incase you have navigation as a `Partial View`, put the above code inside that partial view!

Modify your navigation menu to include the "Orders" link for admin users, just find and fix for orders:
```html
<nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-dark bg-dark">
    <div class="container">
        @Html.ActionLink("Sports Store", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
        <button type="button" class="navbar-toggler" data-bs-toggle="collapse" data-bs-target=".navbar-collapse" title="Toggle navigation" aria-controls="navbarSupportedContent"
                aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse d-sm-inline-flex justify-content-between">
            <ul class="navbar-nav flex-grow-1">
                <li>@Html.ActionLink("Home", "Index", "Home", new { area = "" }, new { @class = "nav-link" })</li>
                <li>@Html.ActionLink("Products", "Index", "Product", new { area = "" }, new { @class = "nav-link" })</li>
                @if (isAdmin)
                {
                    <li>@Html.ActionLink("Orders", "Index", "Order", new { area = "" }, new { @class = "nav-link" })</li>
                }
                <li>@Html.ActionLink("About", "About", "Home", new { area = "" }, new { @class = "nav-link" })</li>
                <li>@Html.ActionLink("Contact", "Contact", "Home", new { area = "" }, new { @class = "nav-link" })</li>
                @if (Model.CartHasItems)
                {
                    <li><a href="/Cart">Cart</a></li>
                }
            </ul>
             @Html.Partial("_LoginPartial")
        </div>
    </div>
</nav>
```
#### Adjust the above code according to your requirements