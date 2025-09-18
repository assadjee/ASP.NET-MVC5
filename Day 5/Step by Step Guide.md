# Step-by-Step Guide to Build the Vidly Project

## Overview

This guide will walk you through the steps to build the Vidly project, a movie rental application using ASP.NET MVC 5. You'll learn to implement key features such as user authentication, movie management, and customer rentals.

## Prerequisites

- Basic understanding of C# and ASP.NET MVC
- Visual Studio installed
- SQL Server or LocalDB for database management

## Step 1: Set Up the Project

1. **Create a New ASP.NET MVC Project**
   - Open Visual Studio.
   - Select "File" > "New" > "Project".
   - Choose "ASP.NET Web Application" and name it `Vidly`.
   - Select the "MVC" template and click "Create".

2. **Install Required NuGet Packages**
   - Open the Package Manager Console.
   - Install Entity Framework:
     ```bash
     Install-Package EntityFramework
     ```
   - Install other necessary packages as needed (e.g., Bootstrap).
3. **Change the Theme**
    - Search **Bootswatch** on google
    - Get the theme of your choice (I choose **Lumen**)
    - Add to the Stylesheets
## Step 2: Create the Data Models

1. **Create the Movie Model**
   - In the `Models` folder, create a `Movie.cs` file:
     ```csharp
     public class Movie
     {
         public int Id { get; set; }
         public string Name { get; set; }
         public DateTime ReleaseDate { get; set; }
         public DateTime DateAdded { get; set; }
         public int NumberInStock { get; set; }
         public Genre Genre { get; set; }
     }
     ```

2. **Create the Genre Model**
   - Create a `Genre.cs` file in the same folder:
     ```csharp
     public class Genre
     {
         public int Id { get; set; }
         public string Name { get; set; }
     }
     ```
3. **Create the Customer Model**
- Create a `Customer.cs` file in the same folder:
```csharp
 public class Customer
 {
     public int Id { get; set; }
     [Required]
     [StringLength(255)]
     public string Name { get; set; }
     [DisplayName("Date of Birth")]
     [Min18yrsIfAMember]
     public DateTime? BirthDate { get; set; }
     public bool IsSubscribedToNewsletter { get; set; }
     public MembershipType MembershipType { get; set; }
     [Display(Name = "Membership Type")]
     public byte MembershipTypeId { get; set; }

 }
```
3. **Create the Rental Model**
- Create a `Rental.cs` file in the same folder:
```csharp
 public class Rental
{
    public int Id { get; set; }
    [Required]
    public Movie Movie { get; set; }
    [Required]
    public Customer Customer { get; set; }
    public DateTime DateRented { get; set; }
    public DateTime? DateReturned { get; set; }
}
```
4. **Create the Membership Type Model**
- Create a `MembershipType.cs` file in the same folder:
```csharp
 public class MembershipType
{
    public byte Id { get; set; }
    public string Name { get; set; }
    public short SignUpFee { get; set; }
    public byte DurationInMonth { get; set; }
    public byte DiscountRate { get; set; }
    public static readonly byte Unknown = 0;
    public static readonly byte PayAsYouGo = 1;
}
```

## Step 3: Set Up the Database Context

1. **Create the ApplicationDbContext**
   - In the `Models` folder, create an `ApplicationDbContext.cs` file:
     ```csharp
     using System.Data.Entity;

     public class ApplicationDbContext : DbContext
     {
         public ApplicationDbContext() : base("DefaultConnection") { }

         public DbSet<Movie> Movies { get; set; }
         public DbSet<Genre> Genres { get; set; }
     }
     ```

2. **Configure Connection String**
   - In `Web.config`, add the connection string:
     ```xml
     <connectionStrings>
         <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Vidly;Integrated Security=True;" providerName="System.Data.SqlClient" />
     </connectionStrings>
     ```

## Step 4: Create the Database

1. **Enable Migrations**
   - In the Package Manager Console, run:
     ```bash
     Enable-Migrations
     ```

2. **Create the Initial Migration**
   - Run:
     ```bash
     Add-Migration InitialCreate
     ```

3. **Update the Database**
   - Run:
     ```bash
     Update-Database
     ```

## Step 5: Create the Controllers

1. **Create the Movies Controller**
   - Right-click on the `Controllers` folder, select "Add" > "Controller".
   - Choose "MVC 5 Controller with views, using Entity Framework".
   - Select `Movie` and `ApplicationDbContext` as the model and data context.
   - Name it `MoviesController`.

## Step 6: Create Views

1. **Index View**
   - The `Index.cshtml` view will automatically be generated. Modify it to display movie details:
     ```html
     @model IEnumerable<Vidly.Models.Movie>

     <h2>Movies</h2>
     <table class="table">
         <thead>
             <tr>
                 <th>Name</th>
                 <th>Release Date</th>
                 <th>Number in Stock</th>
             </tr>
         </thead>
         <tbody>
         @foreach (var movie in Model)
         {
             <tr>
                 <td>@movie.Name</td>
                 <td>@movie.ReleaseDate.ToShortDateString()</td>
                 <td>@movie.NumberInStock</td>
             </tr>
         }
         </tbody>
     </table>
     ```

## Step 7: Set Up User Authentication

1. **Install Identity Framework**
   - In the Package Manager Console, run:
     ```bash
     Install-Package Microsoft.AspNet.Identity.EntityFramework
     ```

2. **Create Identity Models**
   - Create classes in the `Models` folder for user management (e.g., `ApplicationUser`).

3. **Create Account Controller**
   - Add user registration and login functionalities.

## Step 8: Implement Rental Management

1. **Create Rental Model**
   - Implement a model for managing rentals, including properties like `CustomerId`, `MovieId`, and rental dates.

2. **Create Rentals Controller**
   - Implement CRUD operations for handling rentals.

## Step 9: Testing the Application

1. **Run the Application**
   - Start the application in Visual Studio.
   - Test user registration, movie management, and rental functionalities.

2. **Debugging**
   - Use breakpoints and logging to troubleshoot any issues.

## Performance Optimization
Learn about the performance optimization 

## Building a Feature Systematically (end to end)
We'll learn how to build a feature systematically end to end. 
## Deployement