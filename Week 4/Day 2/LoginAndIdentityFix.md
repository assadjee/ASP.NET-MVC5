## Register Identity and inject using unity.config

### Update the `unity.config` and replace the available code with following:

```c#
public static void RegisterComponents()
{
    var container = new UnityContainer();
    // Register the ApplicationDbContext
    container.RegisterType<ApplicationDbContext>(new HierarchicalLifetimeManager());

    // Register UserManager and SignInManager
    container.RegisterType<IUserStore<ApplicationUser>, UserStore<ApplicationUser>>();
    container.RegisterType<UserManager<ApplicationUser>>();// new HierarchicalLifetimeManager());
    container.RegisterType<SignInManager<ApplicationUser, string>>(new HierarchicalLifetimeManager());
    // Register RoleStore and RoleManager
    container.RegisterType<IRoleStore<IdentityRole, string>, RoleStore<IdentityRole>>();
    container.RegisterType<RoleManager<IdentityRole>>();
    // Register controllers
    container.RegisterType<AccountController>();
    
    // Register your types here
    container.RegisterType<IUnitOfWork, UnitOfWork>();
    container.RegisterType<IProductRepository, ProductRepository>();
    container.RegisterType<IOrderRepository, OrderRepository>();
    container.RegisterType<IProductService, ProductService>();
    container.RegisterType<IOrderService, OrderService>();

    DependencyResolver.SetResolver(new UnityDependencyResolver(container));
}
```

### Comment out the following constructor in AccountController: 
```c#
//public AccountController(ApplicationUserManager userManager, ApplicationSignInManager signInManager )
//{
//    UserManager = userManager;
//    SignInManager = signInManager;
//}
```