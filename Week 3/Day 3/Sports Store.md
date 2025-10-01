# Sports Store Application Step by Step Guide

Here’s a detailed step-by-step guide for developing a real-world ASP.NET MVC Sports Store application. This guide covers the essential phases, including planning, setup, development, and deployment.

### Step 1: Project Planning
1. Define Requirements
Identify Features:
- User registration and authentication
- Product catalog
- Shopping cart
- Order management
- Admin panel for managing products
### 2. Create User Stories
- As a user, I want to browse products.
- As a user, I want to add products to my cart.
- As an admin, I want to add/edit/delete products.
### 3. Design Database Schema
#### Entities:
- User
- Product
- Order
- Cart
#### Relationships:
- Users can have multiple Orders.
- Orders can have multiple Products.

### Implementation



### Step 1: Create the Web Application Project

1. **Right-click on the Solution** in Solution Explorer.
2. Select **"Add" > "New Project."**
3. Choose **"ASP.NET Web Application (.NET Framework)."**
4. **Name it** (e.g., `SportsStore.Web`).
5. Click **"OK."**
6. Choose the **"MVC"** template and click **"Create."**

## 2: Creating the Domain Models

### Define Domain Classes (Entities)
#### Product Model 
 
 - create `Product.cs`

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
    public string Description { get; set; }
    public string ImageUrl { get; set; }
}
```
#### Order Model
- create `Order.cs`
```csharp
public class Order
{
    public int Id { get; set; }
    public string UserId { get; set; }
    public DateTime OrderDate { get; set; }
    public List<Product> Products { get; set; }
}
```
### Add following DbSets to ApplicationDBContext (inside `IdentityModels.cs`)

```csharp
    public DbSet<Product> Products { get; set; }
    public DbSet<Order> Orders { get; set; }
```


#### Configuring the Web Application
##### Add Connection String
- Open Web.config in SportsStore.Web.
Add the following connection string:
```xml
<connectionStrings>
    <add name="SportsStoreContext"
         connectionString="Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=SportsStore;Integrated Security=True"
         providerName="System.Data.SqlClient" />
</connectionStrings>
```
##### Enable Migrations
In the Package Manager Console, select the SportsStore.Data project.

```bash
Enable-Migrations
Add-Migration InitialCreate
Update-Database
```

### Add Seed Data for Products
- Add Migration SeedProducts
- Add `INSERT` SQL Queries to add few products.

### Update the UI Layer
#### Create Controllers and Views based on Controllers
- In the `Controllers` folder of the `SportsStore.Web` project, create a new controller named `ProductsController`.

```csharp
using System.Web.Mvc;
using SportsStore.Web.Repositories;

public class ProductsController : Controller
{
    private readonly IProductRepository _productRepository;

    public ProductsController()
    {
        _productRepository = new ProductRepository();
    }

    public ActionResult Index()
    {
        var products = _productRepository.GetAllProducts();
        return View(products);
    }

    public ActionResult Details(int id)
    {
        var product = _productRepository.GetProductById(id);
        return View(product);
    }
}
```
- Create `index.html` view, in `Views/Products` 