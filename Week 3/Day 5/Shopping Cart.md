# ASP.NET MVC Shopping Cart View

## 1. ViewModel for Cart Items

Define a ViewModel to represent the cart item details.

```csharp
public class CartItemViewModel
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string ImageUrl { get; set; }
    public int Quantity { get; set; }
    public decimal Price { get; set; }
    public decimal Total => Quantity * Price; // Calculate total price for the item
}
```

## 2. Cart Service

Create a service to handle cart operations.

```csharp
public interface ICartService
{
    IEnumerable<CartItemViewModel> GetCartItems();
    void RemoveFromCart(int productId);
}
```
```csharp
public class CartService : ICartService
{
    private readonly IUnitOfWork _unitOfWork;

    public CartService(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
    }

    public IEnumerable<CartItemViewModel> GetCartItems()
    {
        // Fetch cart items from the database
        var cartItems = _context.CartItems // Assume CartItems is a DbSet in your DbContext
            .Select(item => new CartItemViewModel
            {
                Id = item.Id,
                Name = item.Product.Name, // Assuming there's a navigation property to Product
                ImageUrl = item.Product.ImageUrl, // Assuming Product has an ImageUrl property
                Quantity = item.Quantity,
                Price = item.Product.Price // Assuming Product has a Price property
            })
            .ToList();

        return cartItems;
    }
    public void RemoveFromCart(int productId)
    {
        // Logic to remove the item from the cart
    }
}
```

## 3. Shopping Cart Controller

Implement the controller to manage cart actions.

```csharp
public class CartController : Controller
{
    private readonly ICartService _cartService;

    public CartController(ICartService cartService)
    {
        _cartService = cartService;
    }

    public ActionResult Index()
    {
        var cartItems = _cartService.GetCartItems();
        return View(cartItems);
    }

    public ActionResult RemoveFromCart(int id)
    {
        _cartService.RemoveFromCart(id);
        return RedirectToAction("Index");
    }
}
```

## 4. View for Shopping Cart

Create the view using Bootstrap for responsive design.

```html
@model IEnumerable<CartItemViewModel>

@{
    ViewBag.Title = "Shopping Cart";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
</head>
<body>
    <div class="container mt-5">
        <h1 class="text-center">Shopping Cart</h1>
        <table class="table table-striped">
            <thead>
                <tr>
                    <th>Product</th>
                    <th>Quantity</th>
                    <th>Price</th>
                    <th>Total</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                @foreach (var item in Model)
                {
                    <tr>
                        <td>
                            <img src="@item.ImageUrl" alt="@item.Name" style="width: 50px; height: auto;" />
                            @item.Name
                        </td>
                        <td>@item.Quantity</td>
                        <td>@item.Price.ToString("C")</td>
                        <td>@item.Total.ToString("C")</td>
                        <td>
                            <a href="@Url.Action("RemoveFromCart", "Cart", new { id = item.Id })" class="btn btn-danger btn-sm">Remove</a>
                        </td>
                    </tr>
                }
            </tbody>
        </table>

        <div class="d-flex justify-content-between">
            <h4>Total: @Model.Sum(item => item.Total).ToString("C")</h4>
            <a href="@Url.Action("Checkout", "Cart")" class="btn btn-success">Proceed to Checkout</a>
        </div>
    </div>

<!--REPLACE THE FOLLOWING WITH CODE FROM BUNDLE.CONFIG-->
    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.9.2/dist/umd/popper.min.js"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
</body>
</html>
```

## 5. Dependency Injection Configuration

Make sure to configure dependency injection for `ICartService`in your `RegisterComponents()` method inside `UnityConfig.cs`

```csharp
container.RegisterType<ICartService, CartService>();
```

## Additional Notes

- **Data Source**: In the `CartService`, the `GetCartItems` method currently returns static data. Implement logic to retrieve actual cart items from a session or database.
- **Remove Functionality**: The `RemoveFromCart` method should contain logic to actually remove items from the cart.
- **Responsive Design**: The use of Bootstrap ensures that the cart view is responsive and looks good on various devices.
- **Customizations**: Feel free to add more features, such as updating item quantities, applying discounts, or integrating with a payment gateway.


