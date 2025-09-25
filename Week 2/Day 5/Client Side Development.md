# Client-Side Development and API Calls Using jQuery and DataTables in the Vidly Project

## Overview

In this section, we will explore how to create a dynamic, interactive user interface for managing customers in the Vidly project. We will use jQuery to make API calls and DataTables to enhance the presentation of our customer data.

## What is DataTables?

DataTables is a jQuery plugin that provides advanced interaction controls for HTML tables. It offers features such as pagination, sorting, and filtering, making it an excellent choice for displaying data in a user-friendly manner.

## Implementation Code

### Step 1: Set Up the Project

1. Ensure you have set up and the customer API implemented as described in previous sections.
2. Include jQuery and DataTables in your project. You can do this by adding the following lines to your layout file (e.g., `_Layout.cshtml`):

```html
<!-- jQuery -->
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<!-- DataTables CSS -->
<link rel="stylesheet" href="https://cdn.datatables.net/1.10.21/css/jquery.dataTables.min.css">
<!-- DataTables JS -->
<script src="https://cdn.datatables.net/1.10.21/js/jquery.dataTables.min.js"></script>
```

### Using Bootstrap Dialog Box. How to use them in project Using Bootbox.js Library

#### Overview of Bootbox.js

**What is Bootbox.js?**
Bootbox.js is a small JavaScript library that makes it easy to create custom modal dialogs using the popular Bootstrap framework. 
It provides a simple way to display alerts, prompts, and confirmation dialogs, enhancing user interaction without navigating away from the current page.

#### Purpose of Using Bootbox.js

- **User Experience**: Modals keep users engaged without requiring page reloads or redirections.
- **Simplicity**: Bootbox.js simplifies the creation of modals with a clean API.
- **Customization**: It allows for easy customization of dialog content and buttons.

##### Implementation Code for Bootbox.js

##### Set Up the Project

1. Ensure you have the Vidly project set up with Bootstrap included.
2. Include the Bootbox.js library in your project. Add the following script to your layout file (e.g., `_Layout.cshtml`):

```bash
install-package bootbox
```
Add bootbox reference to the bundle:
In Bundle config file


```csharp
bundles.Add(new Bundle("~/bundles/bootstrap").Include(
          "~/Scripts/bootstrap.js",
           "~/Scripts/bootbox.js"   
           ));
```

#### Install Datatable

```bash
install-package jquery.datatables -version:1.10.11
```

Add the datatable scripts to Bundle:

In Bundle config file


```csharp
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));

        bundles.Add(new ScriptBundle("~/bundles/jqueryval").Include(
                    "~/Scripts/jquery.validate*"));

        // Use the development version of Modernizr to develop with and learn from. Then, when you're
        // ready for production, use the build tool at https://modernizr.com to pick only the tests you need.
        bundles.Add(new ScriptBundle("~/bundles/modernizr").Include(
                    "~/Scripts/modernizr-*"));

        bundles.Add(new Bundle("~/bundles/bootstrap").Include(
                "~/Scripts/bootstrap.js",
                "~/Scripts/bootbox.js"
                ));

        bundles.Add(new ScriptBundle("~/bundles/dtable").Include(
        
            "~/Scripts/datatables/jquery.datatables.js",
            "~/Scripts/datatables/datatables.bootstrap.js",
            "~/Scripts/typeahead.bundle.js"));

        bundles.Add(new StyleBundle("~/Content/css").Include(
                "~/Content/bootstrap.css",
                "~/Content/datatables/css/dataTables.bootstrap4.css",
                "~/Content/typeahead.css",
                "~/Content/site.css"));
    }    
```

- Update the reference to the `Shared Layout` page
```html
@Scripts.Render("~/bundles/lib")
```
### Step 2: Customer View
Update the customers Index view for displaying and managing customers. 

```html
@using Vidly.Models
@model List<Vidly.Models.Customer>
@{
    ViewBag.Title = "Customers";
    Layout = "~/Views/Shared/_Layout.cshtml";
}
<h2>Customers</h2>
@*@{ Commented because we are using api to get the customers
        if (!Model.Any())
        {
            <p>We don't have any customers yet.</p>
        }
    }

else{

}*@
<table id="customers" class="table">
    <thead>
        <tr>
            <th scope="col">Customer</th>
            <th scope="col">Membership Type</th>
            <th scope="col">Delete</th>
        </tr>
    </thead>
    <tbody>
        @*@foreach (var customer in Model)
            {
                <tr scope="row">

                    <td>@Html.ActionLink(customer.Name, "Edit", "Customers", new {id=@customer.Id}, null)</td>
                    <td>@customer.MembershipType.Name</td>
                    <td>
                        <button data-customer-id="@customer.Id" class="btn-link js-delete">Delete</button>
                    </td>
                </tr>

            }*@

    </tbody>
</table>
@section scripts
{
    <script>
        $(function () {
            var table = $("#customers ").DataTable({
                ajax: {
                    url: "/api/customers",
                    dataSrc: ""
                },
                columns: [
                    {
                        data: "name",
                        render: function (data, type, customer) {
                            return "<a href='/customers/edit/" + customer.id + "'>" + customer.name + "</a>";

                        }
                    },
                    {
                        data: "membershipType.name"
                    },
                    {
                        data: "id",
                        render: function (data) {
                            return "<button class='btn-link js-delete' data-customer-id=" + data + ">Delete</button>";
                        }

                    }
                ]
            });
            $("#customers ").on("click",
                ".js-delete",
                function () {
                    var button = $(this);
                    bootbox.confirm("Are you sure you want to delete this customer?",
                        function (result) {
                            if (result) {
                                $.ajax({
                                    url: "/api/customers/" + button.attr("data-customer-id"),
                                    method: "DELETE",
                                    success: function () {
                                        table.row(button.parents("tr")).remove().draw();
                                    }
                                });
                            }
                        });

                });
        });
    </script>
}
```