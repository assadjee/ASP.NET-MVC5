# Integrating DataTables with AJAX and Bundling in ASP.NET MVC

## Introduction

This guide demonstrates how to integrate DataTables into an ASP.NET MVC application using AJAX calls to load data dynamically. CSS and JS files are managed using the `BundleConfig` class to improve performance.


## Step 1: Install Required Packages

### 1.1 Using NuGet Package Manager

1. Open the **Package Manager Console** in Visual Studio.
2. Run the following commands to install jQuery and DataTables:

   ```bash
   Install-Package jQuery
   Install-Package DataTables.Mvc

## Step 2: Configure BundleConfig
### 2.1 Add CSS and JS Bundles
Open BundleConfig.cs in the App_Start folder.
Add the following bundles for DataTables:


```csharp
            // DataTables CSS
            bundles.Add(new StyleBundle("~/Content/datatables").Include(
                      "~/Content/DataTables/css/jquery.dataTables.min.css"));

            // jQuery and DataTables JS
            bundles.Add(new ScriptBundle("~/bundles/datatables").Include(
                        "~/Scripts/jquery-3.6.0.min.js",
                        "~/Scripts/DataTables/jquery.dataTables.min.js"));
 ```

## Step 3: Modify the View
### 3.1 Open Views/Products/Index.cshtml.
Update the view to include the bundles and set up the HTML table.
```html
@model IEnumerable<YourApp.Model.Product>

@{
    ViewBag.Title = "Products";
}

<h2>Products</h2>

<!-- Include DataTables CSS -->
@Styles.Render("~/Content/datatables")

<table id="productsTable" class="display">
    <thead>
        <tr>
            <th>Name</th>
            <th>Price</th>
            <th>Description</th>
            <th>Action</th>
        </tr>
    </thead>
    <tbody>
        <!-- Data will be populated by AJAX -->
    </tbody>
</table>

<!-- Include jQuery and DataTables JS -->
@Scripts.Render("~/bundles/datatables")

<script>
    $(document).ready(function() {
        $('#productsTable').DataTable({
            "ajax": {
                "url": "@Url.Action("GetProducts", "Products")", // AJAX call to fetch data
                "dataSrc": ""
            },
            "columns": [
                { "data": "Name" },
                { "data": "Price", "render": $.fn.dataTable.render.number(',', '.', 2, '$') },
                { "data": "Description" },
                { "data": null, "defaultContent": '<a href="#" class="view">View</a>' }
            ]
        });
    });
</script>
```
## Step 4: Create the AJAX Action Method
### 4.1 Create GetProducts Action in  ProductsController.cs.
Add the following action method to return JSON data:
```csharp

        public JsonResult GetProducts()
        {
            var products = _productRepository.GetAllProducts().ToList();
            return Json(products, JsonRequestBehavior.AllowGet);
        }
```

### 4.2 Add Similar Ajax Action Method GetOrders in OrdersController 

```csharp

        public JsonResult GetOrders()
        {
            var orders = _orderRepository.GetAllOrders().ToList();
            return Json(orders, JsonRequestBehavior.AllowGet);
        }
```
