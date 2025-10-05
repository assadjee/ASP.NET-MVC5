# Integrating DataTables with AJAX and Bundling in ASP.NET MVC

## Introduction

To integrate DataTables into SportsStore application, we'll be using AJAX calls to load data dynamically. Note that the CSS and JS files are managed using the `BundleConfig` class to improve performance.


## Step 1: Install Required Packages

### 1.1 Using NuGet Package Manager

1. Open the **Package Manager Console** in Visual Studio.
2. Run the following commands to install jQuery and DataTables:

```bash
install-package jquery.datatables
```

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
                { "data": null, 
                "render": function(data, type, row) {
                        return '<a href="/product/details/" class="btn btn-primary view">View</a>';
                }
                }
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
            var products = _productServices.GetAllProducts().ToList();
            return Json(products, JsonRequestBehavior.AllowGet);
        }
```

### 4.2 Add Similar Ajax Action Method GetOrders in OrdersController 

```csharp

        public JsonResult GetOrders()
        {
            var orders = _orderServices.GetAllOrders().ToList();
            return Json(orders, JsonRequestBehavior.AllowGet);
        }
```

### Order List View
```html
@model IEnumerable<YourApp.Model.Order>

@{
    ViewBag.Title = "Order";
}

<h2>Orders</h2>
 <div class="container mt-5">
        <table id="ordersTable" class="table table-striped">
            <thead>
                <tr>
                    <th>Order ID</th>
                    <th>User ID</th>
                    <th>Order Date</th>
                    <th>Products</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                <!-- DataTable will populate this -->
            </tbody>
        </table>
    </div>

    
    <script>
        $(document).ready(function() {
            $('#ordersTable').DataTable({
                "ajax": {
                    "url": "@Url.Action('GetOrders', 'Orders')",
                    "dataSrc": ""
                },
                "columns": [
                    { "data": "Id" },
                    { "data": "UserId" },
                    { 
                        "data": "OrderDate", 
                        "render": function(data) {
                            return new Date(data).toLocaleDateString(); // Format the date
                        }
                    },
                    { 
                        "data": "Products", 
                        "render": function(data) {
                            return data.map(product => product.Name).join(', '); // Join product names
                        }
                    },
                    { 
                        "data": null, 
                        "render": function(data, type, row) {
                            return '<a href="#" class="btn btn-primary view">View</a>';
                        }
                    }
                ]
            });
        });
    </script>
```

### Order Detail View (Opened when View button is clicked):

```html
@model <YourApp.Model.Order>

@{
    ViewBag.Title = "Order";
}

<h2>Order Details</h2>

<div class="container mt-5">
        <div id="orderDetails" class="mb-3">
            <h5>Order ID: <span id="orderId"></span></h5>
            <h5>User ID: <span id="userId"></span></h5>
            <h5>Order Date: <span id="orderDate"></span></h5>
        </div>

        <table id="productsTable" class="table table-striped">
            <thead>
                <tr>
                    <th>Product Name</th>
                    <th>Price</th>
                    <th>Description</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                <!-- DataTable will populate this -->
            </tbody>
        </table>
    </div>

   <script>
        $(document).ready(function() {
            // Replace with actual order details. You may get this data from your AJAX response.
            const orderDetails = {
                Id: 1,
                UserId: "user123",
                OrderDate: "2023-10-01T00:00:00"
            };

            // Set order details in the above section
            $('#orderId').text(orderDetails.Id);
            $('#userId').text(orderDetails.UserId);
            $('#orderDate').text(new Date(orderDetails.OrderDate).toLocaleDateString());

            // Initialize DataTable for products
            $('#productsTable').DataTable({
                "ajax": {
                    "url": "@Url.Action('GetProductsByOrder', 'Orders', { orderId: orderDetails.Id })", // AJAX call to fetch products for this order
                    "dataSrc": ""
                },
                "columns": [
                    { "data": "Name" },
                    { "data": "Price", "render": $.fn.dataTable.render.number(',', '.', 2, '$') },
                    { "data": "Description" },
                    { 
                        "data": null, 
                        "render": function(data, type, row) {
                            return '<a href="#" class="btn btn-primary view">View</a>';
                        }
                    }
                ]
            });
        });
    </script>
```
### Updated Method to get order detials


```csharp
    [HttpGet]
    public IActionResult GetProductsByOrder(int orderId)
    {
        // Fetch the order from the database
        var order = _context.Orders
            .Include(o => o.Products) // Ensure to include the Products navigation property
            .FirstOrDefault(o => o.Id == orderId);

        if (order == null)
        {
            return NotFound(); // Return a 404 if the order doesn't exist
        }

        // Select the products to return
        var products = order.Products.Select(p => new
        {
            p.Name,
            p.Price,
            p.Description
        }).ToList();

        return Json(products); // Return the products as JSON
    }
```

