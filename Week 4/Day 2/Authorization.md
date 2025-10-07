# ASP.NET MVC 5 – Authentication & Authorization

## Introduction
- **Authentication** → Who you are  
- **Authorization** → What you are allowed to do  
- ASP.NET MVC 5 uses the **ASP.NET Identity Framework** for authentication and role-based authorization.  
- Supports:  
  - Username/password login  
  - Social logins (Google, Facebook, Twitter, Microsoft)  
  - Roles & claims  

---

## Authentication in MVC 5
### Setting up Identity
- When creating a new **MVC project with Individual Accounts**, Visual Studio scaffolds:  
  - `AccountController` → Handles login, logout, registration  
  - `ManageController` → Handles password resets, 2FA, etc.  
  - `ApplicationUser` (in *Models/IdentityModels.cs*) → Extends `IdentityUser`  

### Example: Registering a User
```csharp
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Register(RegisterViewModel model)
{
    if (ModelState.IsValid)
    {
        var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
        var result = await UserManager.CreateAsync(user, model.Password);
        if (result.Succeeded)
        {
            await SignInManager.SignInAsync(user, isPersistent:false, rememberBrowser:false);
            return RedirectToAction("Index", "Home");
        }
        AddErrors(result);
    }
    return View(model);
}
```
### Login / Logout

### Login Example

```csharp
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Login(LoginViewModel model, string returnUrl)
{
    if (!ModelState.IsValid)
        return View(model);

    var result = await SignInManager.PasswordSignInAsync(
        model.Email, model.Password, model.RememberMe, shouldLockout: false);

    switch (result)
    {
        case SignInStatus.Success:
            return RedirectToLocal(returnUrl);
        case SignInStatus.Failure:
        default:
            ModelState.AddModelError("", "Invalid login attempt.");
            return View(model);
    }
}
```

### Logout Example
```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult LogOff()
{
    AuthenticationManager.SignOut(DefaultAuthenticationTypes.ApplicationCookie);
    return RedirectToAction("Index", "Home");
}
```

## Authorization in MVC 5
### Role-Based Authorization

- Add roles using `RoleManager`.

- Decorate controllers or actions with `[Authorize]`.

```csharp
[Authorize(Roles = "Admin")]
public ActionResult AdminOnly()
{
    return View();
}
```
### Restrict by User
```csharp
[Authorize(Users = "admin@example.com")]
public ActionResult SpecialPage()
{
    return View();
}
```

### Allow Anonymous Access

```csharp
[AllowAnonymous]
public ActionResult PublicPage()
{
    return View();
}
```

## Setting Up Roles in SportsStore

- Add the following code in `Register` Action inside `AccountController`

```csharp
public async Task<ActionResult> Register(RegisterViewModel model)
{
    if (ModelState.IsValid)
    {
        var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
        var result = await UserManager.CreateAsync(user, model.Password);
        if (result.Succeeded)
        {
            var roleStore = new RoleStore<IdentityRole>(new ApplicationDbContext());
            var roleManager = new RoleManager<IdentityRole>(roleStore);
            await roleManager.CreateAsync(new IdentityRole("CanManageProducts"));
            await UserManager.AddToRoleAsync(user.Id, "CanManageProducts");
            await SignInManager.SignInAsync(user, isPersistent:false, rememberBrowser:false);
            
            // For more information on how to enable account confirmation and password reset please visit https://go.microsoft.com/fwlink/?LinkID=320771
            // Send an email with this link
            // string code = await UserManager.GenerateEmailConfirmationTokenAsync(user.Id);
            // var callbackUrl = Url.Action("ConfirmEmail", "Account", new { userId = user.Id, code = code }, protocol: Request.Url.Scheme);
            // await UserManager.SendEmailAsync(user.Id, "Confirm your account", "Please confirm your account by clicking <a href=\"" + callbackUrl + "\">here</a>");

            return RedirectToAction("Index", "Home");
        }
        AddErrors(result);
    }

    // If we got this far, something failed, redisplay form
    return View(model);
}

```
- Register new user `admin@store.com` (from the browser) 
- Comment out the code above.
- Create a new Migration SeedUsers.
- Generate and get the scripts from `AspNetUsers`, `AspNetRoles`, and `ASPNetUserRoles`. Put all the scripts inside Sql method in Up method of new migration.
- Delete the user and role records from database in this sequence: `ASPNetUserRoles`, `AspNetRoles` and then `AspNetUsers`.
- Update the database and confirm that the seed data is saved into these tables.   

### Working With roles
- Providing Access to certain views based on user roles. Update the code as we did in Vidly project
- Following code is from Vidly Project:

```csharp
public ActionResult Index()
{
    if(User.IsInRole("CanManageProducts"))
        return View("List", db.Movies.Include(x => x.Genre).ToList());
    return View("ReadOnlyList", db.Movies.Include(x => x.Genre).ToList());
}
```

- Define role based filters to restrict access to authorized users

```csharp
// GET: Products/Create
[Authorize(Roles = "CanManageProducts")]
public ActionResult Create()
{
    // Actual View
}
```

### Making Roles names static and easily maintainable

- create a RoleName class in models 
- define roles in this class

```csharp
public class RoleName
{
    public const string CanManageProducts = "CanManageProducts";
}
```
- update the code based on this RoleNames class `[Authorize(Roles = RoleName.CanManageProducts)]
`.