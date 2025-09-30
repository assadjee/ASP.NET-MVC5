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

### Create Services Folder
- Add following classes inside the Services
#### public class ProductService
```csharp
{
    private readonly SportsStoreContext _context;

    public ProductService(SportsStoreContext context)
    {
        _context = context;
    }

    public IEnumerable<Product> GetAllProducts() => _context.Products.ToList();
    public Product GetProductById(int id) => _context.Products.Find(id);
}
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

### Define Repository Interfaces
- In `Web` Project Add new folder named `Repositories`
- Add new interface named `IProductRepository.cs`
```csharp

    public interface IProductRepository
    {
        IEnumerable<Product> GetAllProducts();
        Product GetProductById(int id);
        void AddProduct(Product product);
        void UpdateProduct(Product product);
        void DeleteProduct(int id);
    }

```

- Add new interface named `IOrderRepository.cs`

```csharp

    public interface IOrderRepository
    {
        IEnumerable<Order> GetAllOrders();
        Order GetOrderById(int id);
        void AddOrder(Order order);
    }

```

### Implement Repository Classes

- Inside `Repositories` folder:
- Add a class named `ProductRepository.cs`
```csharp
uusing System.Collections.Generic;
using System.Linq;

namespace SportsStore.Web.Repositories
{
    public class ProductRepository : IProductRepository
    {
        private readonly List<Product> _products;

        public ProductRepository()
        {
            // Sample data for demonstration
            _products = new List<Product>
            {
                new Product { Id = 1, Name = "Football", Price = 25.00m, Description = "A regulation-size football.", ImageUrl = "/images/football.jpg" },
                new Product { Id = 2, Name = "Tennis Racket", Price = 75.00m, Description = "A high-quality tennis racket.", ImageUrl = "/images/racket.jpg" }
            };
        }

        public IEnumerable<Product> GetAllProducts() => _products;

        public Product GetProductById(int id) => _products.FirstOrDefault(p => p.Id == id);

        public void AddProduct(Product product)
        {
            product.Id = _products.Max(p => p.Id) + 1; // Simple ID generation
            _products.Add(product);
        }

        public void UpdateProduct(Product product)
        {
            var existing = GetProductById(product.Id);
            if (existing != null)
            {
                existing.Name = product.Name;
                existing.Price = product.Price;
                existing.Description = product.Description;
                existing.ImageUrl = product.ImageUrl;
            }
        }

        public void DeleteProduct(int id)
        {
            var product = GetProductById(id);
            if (product != null)
            {
                _products.Remove(product);
            }
        }
    }
}
```

- Add a class named `OrderRepository.cs`

```csharp
using System.Collections.Generic;
using SportsStore.Domain;

namespace SportsStore.Web.Repositories
{
    public class OrderRepository : IOrderRepository
    {
        private readonly List<Order> _orders;

        public OrderRepository()
        {
            _orders = new List<Order>();
        }

        public IEnumerable<Order> GetAllOrders() => _orders;

        public Order GetOrderById(int id) => _orders.FirstOrDefault(o => o.Id == id);

        public void AddOrder(Order order)
        {
            order.Id = _orders.Count + 1; // Simple ID generation
            _orders.Add(order);
        }
    }
}
```

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