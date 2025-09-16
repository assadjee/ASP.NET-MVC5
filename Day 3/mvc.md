# Key Benefits of ASP.NET MVC

In October 2007, Microsoft announced a new MVC Web development platform built on the core ASP.NET platform. This was clearly designed as a direct response to the evolution of technologies such as Rails and as a reaction to the criticisms of Web Forms. The following sections describe how this new platform overcame the limitations of Web Forms and brought ASP.NET back to the cutting edge.

## MVC Architecture

It is important to distinguish between the MVC architectural pattern and the ASP.NET MVC Framework. The MVC pattern is not new—it dates back to 1978 and the Smalltalk project at Xerox PARC—but it has gained enormous popularity today as a pattern for Web applications for several reasons:

- User interaction with an MVC application follows a natural cycle: the user takes an action, and in response, the application changes its data model and delivers an updated view to the user. This cycle repeats, fitting conveniently for Web applications delivered as a series of HTTP requests and responses.
- Web applications necessitate combining several technologies (databases, HTML, and executable code, for example), usually split into a set of tiers or layers. The patterns that arise from these combinations map naturally onto the concepts in MVC.

The ASP.NET MVC Framework implements the MVC pattern and provides greatly improved separation of concerns. By embracing and adapting the MVC pattern, the ASP.NET MVC Framework provides strong competition to Ruby on Rails and similar platforms, bringing the MVC pattern into the mainstream of the .NET world.

## Extensibility

The MVC Framework is built as a series of independent components that satisfy a .NET interface or that are built on an abstract base class. You can easily replace components, such as the routing system, the view engine, and the controller factory, with your own implementation. The MVC Framework gives you three options for each component:

- Use the default implementation of the component as it stands (which should be enough for most applications).
- Derive a subclass of the default implementation to tweak its behavior.
- Replace the component entirely with a new implementation of the interface or abstract base class.

## Tight Control over HTML and HTTP

ASP.NET MVC produces clean, standards-compliant markup. Its built-in HTML helper methods produce standards-compliant output, encouraging you to craft simple, elegant markup styled with CSS. 

If you want to use ready-made widgets for complex UI elements such as date pickers or cascading menus, ASP.NET MVC’s “no special requirements” approach to markup makes it easy to use best-of-breed UI libraries such as jQuery UI or the Bootstrap CSS library. 

ASP.NET MVC-generated pages don’t contain any View State data, making them smaller than typical pages from ASP.NET Web Forms. This economy of bandwidth improves the end-user experience and helps reduce the cost of running a popular web application. 

ASP.NET MVC works in tune with HTTP, allowing you to control the requests passing between the browser and server. AJAX is made easy, and there are no automatic postbacks to interfere with client-side state.

## Testability

The MVC architecture naturally separates different application concerns into independent pieces, making your application maintainable and testable. To support unit testing, the ASP.NET MVC designers made sure each separate piece is structured to meet the requirements of unit testing and mocking tools.

They added Visual Studio wizards to create unit test projects, which can be integrated with open-source unit test tools such as NUnit and xUnit. ASP.NET MVC applications also work well with UI automation testing tools, allowing you to write test scripts that simulate user interactions without needing to guess the generated HTML structure.

## Powerful Routing System

The style of URLs has evolved as Web application technology has improved. ASP.NET MVC uses a feature known as URL routing to provide clean URLs by default. This gives you control over your URL schema and its relationship to your application, offering the freedom to create a pattern of URLs that is meaningful and useful to your users.

## Built on the Best Parts of the ASP.NET Platform

ASP.NET MVC is based on the .NET platform, providing flexibility to write code in any .NET language and access the same API features. Ready-made ASP.NET platform features—such as authentication, membership, roles, profiles, and internationalization—can reduce the amount of code you need to develop and maintain.

## Modern API

ASP.NET MVC 5 is built for .NET 4.5.1, allowing it to take full advantage of recent language and runtime innovations. The MVC Framework’s API methods and coding patterns follow a cleaner, more expressive composition than was possible with earlier platforms.

## ASP.NET MVC Is Open Source

You are free to download the original source code for ASP.NET MVC and modify it as needed. This is invaluable for debugging, as it allows you to step into its code and read the original programmers’ comments. ASP.NET MVC is licensed under the Microsoft Public License, an Open Source Initiative (OSI)–approved license.

## What Do I Need to Know?

To get the most from this book, you should be familiar with the basics of web development, understand how HTML and CSS work, and have a working knowledge of C#. 

## Creating a Simple Data-Entry Application

In the rest of this chapter, I will explore the basic MVC features by building a simple data-entry application. My goal is to demonstrate MVC in action by creating a web app that allows users to electronically RSVP to a New Year’s Eve party with key features such as:

- A home page that shows information about the party
- A form for RSVP
- Validation for the RSVP form
- RSVPs emailed to the party host when complete

### Designing a Data Model

In MVC, the M stands for model, which is the representation of the real-world objects, processes, and rules that define the subject of the application. The model contains the C# objects that make up the universe of the application and the methods that manipulate them.

```
 public class GuestResponse
 {
     public string Name { get; set; }
     public string Email { get; set; }
     public string Phone { get; set; }
     public bool? WillAttend { get; set; }
 }
```

### View:

#### Drop down change to:

```
     @Html.DropDownListFor(model => model.WillAttend, new[]
{
    new SelectListItem(){Text = "Yes, I'll be there", Value = bool.TrueString},
    new SelectListItem(){Text = "No, I can not come", Value = bool.TrueString},

})
```


### Controller
What to do with the data when user submits it
#### HttpPost Method:

``` csharp
[HttpPost]
public ViewResult RsvpForm(GuestResponse guestResponse)
{
    // TODO: Email response to the party organizer
    return View("Thanks", guestResponse);
}
```

#### Using Model Binding

Using Model Binding
The first overload of the RsvpForm action method renders the same view as before–the RsvpForm.cshtml file–to generate
the form shown in Figure 2-18.
The second overload is more interesting because of the parameter, but given that the action method will
be invoked in response to an HTTP POST request, and that the GuestResponse type is a C# class, how are the two
connected?
The answer is model binding, an extremely useful MVC feature whereby incoming data is parsed and the key/value
pairs in the HTTP request are used to populate properties of domain model types.


#### Rendering Other Views

```csharp
...
return View("Thanks", guestResponse);
...
```
```
<div>
<h1>Thank you, @Model.Name!</h1>
@if (Model.WillAttend == true) {
@:It's great that you're coming. The drinks are already in the fridge!
} else {
@:Sorry to hear that you can't make it, but thanks for letting us know.
}
</div>
```


### Adding Validation
I am now in a position to add validation to my application. Without validation, users could enter nonsense data or even
submit an empty form. In an MVC application, validation is typically applied in the domain model, rather than in the
user interface. This means that I am able to define validation criteria in one place and have it take effect anywhere in the
application that the model class is used. ASP.NET MVC supports declarative validation rules defined with attributes from
the System.ComponentModel.DataAnnotations namespace

```csharp
using System.ComponentModel.DataAnnotations;
namespace PartyInvites.Models {
public class GuestResponse {
[Required(ErrorMessage = "Please enter your name")]
public string Name { get; set; }
[Required(ErrorMessage = "Please enter your email address")]
[RegularExpression(".+\\@.+\\..+",
ErrorMessage = "Please enter a valid email address")]
public string Email { get; set; }
[Required(ErrorMessage = "Please enter your phone number")]
public string Phone { get; set; }
[Required(ErrorMessage = "Please specify whether you'll attend")]
public bool? WillAttend { get; set; }
}
}
```


#### Validation in Controller

```csharp 
[HttpPost]
public ViewResult RsvpForm(GuestResponse guestResponse) {
if (ModelState.IsValid) {
// TODO: Email response to the party organizer
return View("Thanks", guestResponse);
} else {
// there is a validation error
return View();
}
}
```

#### In View add the following inside the form:

```
@using (Html.BeginForm()) {
@Html.ValidationSummary()
```


#### Highlighting Invalid Fields
- Step 1:
Add Validation error class
```
<input class="input-validation-error" data-val="true" data-val-required="Please enter your name"
id="Name" name="Name" type="text" value="" />
```

- Step 2: Add Styles.css file to Content folder
- Step 3: Add following css:

```css
.field-validation-error {color: #f00;}
.field-validation-valid { display: none;}
.input-validation-error { border: 1px solid #f00; background-color: #fee; }
.validation-summary-errors { font-weight: bold; color: #f00;}
.validation-summary-valid { display: none;}
``` 

-Step 5: Add css to the view:
```
<link rel="stylesheet" type="text/css" href="~/Content/Styles.css" />
```