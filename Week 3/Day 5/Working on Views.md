# Working with Views

## Using Card Layout for Products (to show for clients) 

Use Bootstrap to create the responsive card layout.
```html
<div class="container mt-5">
        <h1 class="text-center">Order List</h1>
        <div class="row">
            @foreach (var product in Model)
            {
                <div class="col-md-4 mb-4">
                    <div class="card">
                        <img src="@product.ImageUrl" class="card-img-top" alt="@product.Name">
                        <div class="card-body">
                            <h5 class="card-title">@product.Name</h5>
                            <p class="card-text">@product.Description</p>
                            <p class="card-text"><strong>Price: @product.Price.ToString("C")</strong></p>
                            <a href="@Url.Action("AddToCart", "Cart", new { id = product.Id })" class="btn btn-primary">Add to Cart</a>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>