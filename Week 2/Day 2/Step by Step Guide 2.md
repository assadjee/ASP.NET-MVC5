# Step-by-Step Guide to Build the Vidly Project (Day 2) Continued...

## Overview

This guide will walk you through the steps to build the Vidly project, a movie rental application using ASP.NET MVC 5. You'll learn to implement key features such as user authentication, movie management, and customer rentals.

## Today's Objectives:
- Filling in data to the database
- Create Controllers and Views
- Validation
- Custom Validation (Implementing the business rules)

## Coding Challenges:
### Plopulate Genre table using the migrations
1. Add dbSet of Genre to the DbContext (if not added yet)
2. Add Following are the possible values for the Genre:
   - Action
   - Adventure
   - Comedy
   - Crime
   - Documentary
   - Drama
   - Fantasy
   - Horror
3. Use Migration to populate these values



## Create the Controllers and the Views from Models ( Use scaffolding and create Views along with Controller)
Create the Controllers using scaffolding for each of the models.
Note that the Views are created automatically.

- Movies
- Customers


1. . **Create the Movies Controller**
   - Right-click on the `Controllers` folder, select "Add" > "Controller".
   - Choose "MVC 5 Controller with views, using Entity Framework".
   - Select `Movie` and `ApplicationDbContext` as the model and data context.
   - Name it `MoviesController`.

Repeat the same steps for each of the Models.


### **Views based on the Controller**

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


## Challenge: Update the movies and Add Genre to them
Use the following to do so:
1. Add Genre to the Movies Model ` public Genre Genre { get; set; }`
2. Update Movies Table using Migrations
3. Add Genre values to the Movies using Migration
4. Make necessary Changes to the Movies Controller and the corresponding Views

### References - Entity Framework:

#### LINQ Extension Methods
```csharp
_context.Movies.Where(m => m.GereId == 1);
_context.Movies.Single(m => m.Id == 1);
_context.Movies.SingleOrDefault(m => m.Id == 1);
_context.Movies.ToList();

//Eager Loading
_context.Movies.Include(m => m.Genre);
```

## Forms, Some insights

### Building Forms:

```html
@using (Html.BeginForm(“action”, “controller”))
{
<div class=“form‐group”>
    @Html.LabelFor(m=>m.Name)
    @Html.TextBoxFor(m=>m.Name,new{@class=“form-control”})
</div>
<button type=“submit” class=“btn btn-primary”>Save</button>
}
```

#### date field

```
 @Html.EditorFor(model => model.BirthDate,"{0:dd-MM-yyyy}", new { htmlAttributes = new { @class = "form-control", type="date" } })
```
#### Displaying date

```c
@if(item.BirthDate.HasValue)
{

@item.BirthDate.Value.ToString("dd-MM-yyyy")
}
else
{
<span>&nbsp;</span>    
}
```
#### checkbox

```
Markup for Checkbox Fields
<div class=“checkbox”>
@Html.CheckBoxFor(m => m.IsSubscribed) Subscribed?
</div>
```
#### Drop-down Lists

```
@Html.DropDownListFor(m =>m.TypeId,newSelectList(Model.Types,“Id”,“Name”),“”,new{@class=“form-­‐control”})     
```

#### Saving the data

```csharp
[HttpPost]
public ActionResult
Save(Customer customer)
{
   if (customer.Id == 0)
      _context.Customers.Add(customer);
   else
   {
      var customerInDb = _context.Customers.Single(c.Id
                           == customer.Id);
   }
   _context.SaveChanges();
   return RedirectToAction(“Index”, “Customers”)
}
//…
```

#### Hidden Fields

Required when updating data.
`@Html.HiddenFor(m =>m.Customer.Id)`


## Implementing Validations

### Custom Validation
Apply validation for the business rule that the customer should be 18 years old if wants to be a member.

#### Steps:
1. Add new class `Min18YearsIfAMember` (For validation Attribute)
2. Add the Attribute to the Model
3. Add the following code into the newly added class:

```csharp
protected override ValidationResult IsValid(object value, ValidationContext validationContext)
{
    var customer = (Customer)validationContext.ObjectInstance;
    if (customer.MembershipTypeId == 0 || customer.MembershipTypeId == 1)
        return ValidationResult.Success;
    if (customer.BirthDate == null)
        return new ValidationResult("Birthdate is required");
    var age = DateTime.Today.Year - customer.BirthDate.Value.Year;
    return (age >= 18) ? ValidationResult.Success : new ValidationResult("Customer should be at least 18 years old to be a member");                       
}
```

##### Refactoring Magic Numbers
Get rid of magic numbers (0, 1) to make the code more readable. In `MembershipType` Model, explicitly define the membership types to make the code more maintainable. 

```csharp
public static readonly byte Unknown = 0;
public static readonly byte PayAsYouGo = 1;
```
Making them readonly to avoid accidental changes in any other place in the code. Now in the validation class, replace 0 and 1 in the Validation Attribute.
## Client Side Validation
By default the client side validation is not enabled.
```
@section scripts{
   @Scripts.Render("~/bundles/jqueryval")
}
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