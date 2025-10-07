# Show order in the Navigation bar:
## Step 1: Update Layout View to Show "Orders" Menu for Admin
In shared layout file (_Layout.cshtml), check if the logged-in user is an admin and conditionally display the "Orders" menu item.

- Inject UserManager and RoleManager
Add the following at the top of your layout file:
csharp

```csharp
@using Microsoft.AspNet.Identity
@using Microsoft.AspNet.Identity.Owin
@inject Microsoft.AspNet.Identity.UserManager<ApplicationUser> UserManager
@inject Microsoft.AspNet.Identity.RoleManager<IdentityRole> RoleManager
```
## Check User Role
Check if the user is in the "Admin" role:

```csharp
@{
    var isAdmin = User.Identity.IsAuthenticated && UserManager.IsInRole(User.Identity.GetUserId(), RoleName.CanManageProducts);
}
```

### Update Navigation Menu
Modify your navigation menu to include the "Orders" link for admin users:
```html
<nav>
    <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/Products">Products</a></li>
        @if (Model.CartHasItems)
        {
            <li><a href="/Cart">Cart</a></li>
        }
        @if (isAdmin)
        {
            <li><a href="/Orders">Orders</a></li>
        }
    </ul>
</nav>
```
#### Adjust the above code according to your requirements