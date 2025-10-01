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
#### Step 1: Create a Migration

1. Open the *Package Manager Console* in Visual Studio.
2. Run the following command to create a new migration:

   ```bash
   Add-Migration SeedProducts
   ```

#### Step 2: Modify the Migration

Open the newly created migration file and modify the Up method to insert your initial products.

#### Example Migration

Here's how you can structure your migration to include an image URL:

```csharp
using System.Data.Entity.Migrations;

namespace SportStore.Migrations
{
    public partial class SeedProducts : DbMigration
    {
        public override void Up()
        {
            // Insert initial products with image URLs
            Sql("INSERT INTO Products (Name, Price, Description, ImageUrl) VALUES ('Product 1', 10.00, 'Description for Product 1', 'http://example.com/images/product1.jpg')");
            Sql("INSERT INTO Products (Name, Price, Description, ImageUrl) VALUES ('Product 2', 20.00, 'Description for Product 2', 'http://example.com/images/product2.jpg')");
            Sql("INSERT INTO Products (Name, Price, Description, ImageUrl) VALUES ('Product 3', 30.00, 'Description for Product 3', 'http://example.com/images/product3.jpg')");
            Sql("INSERT INTO Products (Name, Price, Description, ImageUrl) VALUES ('Product 4', 40.00, 'Description for Product 4', 'http://example.com/images/product4.jpg')");
            Sql("INSERT INTO Products (Name, Price, Description, ImageUrl) VALUES ('Product 5', 50.00, 'Description for Product 5', 'http://example.com/images/product5.jpg')");
        }

        public override void Down()
        {
            // Remove the products if rolling back
            Sql("DELETE FROM Products WHERE Name IN ('Product 1', 'Product 2', 'Product 3', 'Product 4', 'Product 5')");
        }
    }
}
```

#### Step 3: Update the Database

After modifying the migration, apply it to your database:

1. In the *Package Manager Console*, run:

```bash
Update-Database
```
