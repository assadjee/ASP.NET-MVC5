# Coding Conventions for ASP.NET MVC

## Introduction

Adhering to coding conventions is crucial for maintaining code quality and readability in ASP.NET MVC applications. This document outlines best practices and conventions that developers should follow.

## 1. Naming Conventions

### 1.1. Classes

- **Controllers**: Use the suffix `Controller` for all controller classes.  
  **Example**: `HomeController`, `AccountController`

- **Models**: Use descriptive names that represent the data.  
  **Example**: `UserModel`, `ProductModel`

### 1.2. Methods

- **Action Methods**: Use PascalCase for method names.  
  **Example**: `Index()`, `Create()`

- **Private Methods**: Use camelCase for private methods.  
  **Example**: `private void logAction()`

### 1.3. Views

- **View Files**: Use the same name as the action method, with the `.cshtml` extension.  
  **Example**: `Index.cshtml`, `Create.cshtml`

## 2. Folder Structure

### 2.1. Controllers

Place all controller classes in the `Controllers` folder.

### 2.2. Models

Place all model classes in the `Models` folder.

### 2.3. Views

Organize views in subfolders named after their respective controllers.  
**Example**: `Views/Home/Index.cshtml`, `Views/Account/Login.cshtml`

## 3. Action Methods

### 3.1. Return Types

Always return an `ActionResult` from action methods.

### 3.2. HTTP Verbs

Use the appropriate HTTP verb attributes for action methods:

- `[HttpGet]` for retrieving data.
- `[HttpPost]` for submitting data.
- `[HttpPut]` and `[HttpDelete]` when applicable.

### 3.3. Model Binding

Use model binding to pass complex types and avoid using `Request.Form` directly.

```csharp
public ActionResult Create(UserModel user)
{
    if (ModelState.IsValid)
    {
        // Save user
    }
    return View(user);
}
```

## 4. Validation

### 4.1. Data Annotations

Use data annotations in models for validation.  
**Example**:

```csharp
public class UserModel
{
    [Required]
    [StringLength(100)]
    public string Name { get; set; }

    [EmailAddress]
    public string Email { get; set; }
}
```

### 4.2. ModelState

Always check `ModelState.IsValid` before processing the data.

## 5. Views

### 5.1. Razor Syntax

Use Razor syntax for embedding C# code in views. Keep logic minimal in views.

### 5.2. HTML Helpers

Use HTML helpers for generating form elements and links.  
**Example**:

html
@Html.TextBoxFor(m => m.Name)
@Html.ActionLink("Home", "Index", "Home")


## 6. Comments and Documentation

### 6.1. Code Comments

Use comments to explain complex logic but avoid obvious comments.

### 6.2. XML Documentation

Use XML comments for public methods and classes to generate documentation.

## 7. Consistency

Maintain consistency in coding style across the project. This includes indentation, spacing, and bracket placement.

## Conclusion

Following these coding conventions will help maintain the readability and maintainability of ASP.NET MVC applications. Consistency is key, and adopting these practices will lead to higher-quality code.