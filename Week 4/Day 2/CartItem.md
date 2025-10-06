# SportsStore Application Shopping Cart

Shopping cart in the SportsStore application  allows users to add products, remove items, and adjust quantities.

## Step 1: Define Your Models

Assuming you have the following models for CartItem:

```csharp
public class CartItem
{
    public int Id { get; set; }
    public Product Product { get; set; }
    public int Quantity { get; set; }
}
```

## Step 2: Create the Cart Service
Create a service that will manage the shopping cart functionality.

```csharp
public interface ICartService
{
    void AddToCart(int productId, int quantity);
    void RemoveFromCart(int productId);
    void UpdateQuantity(int productId, int quantity);
    IEnumerable<CartItem> GetCartItems();
}
```

```c#
public class CartService : ICartService
{
    private readonly IUnitOfWork _unitOfWork;
    private readonly List<CartItem> _cartItems;

    public CartService(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
        _cartItems = new List<CartItem>();
    }

    public void AddToCart(int productId, int quantity)
    {
        var product = _unitOfWork.Products.GetProductById(productId);
        if (product != null)
        {
            var cartItem = _cartItems.FirstOrDefault(ci => ci.Product.Id == product.Id);
            if (cartItem != null)
            {
                cartItem.Quantity += quantity;
            }
            else
            {
                _cartItems.Add(new CartItem
                {
                    Product = product,
                    Quantity = quantity
                });
            }
        }
    }

    public void RemoveFromCart(int productId)
    {
        var cartItem = _cartItems.FirstOrDefault(ci => ci.Product.Id == productId);
        if (cartItem != null)
        {
            _cartItems.Remove(cartItem);
        }
    }

    public void UpdateQuantity(int productId, int quantity)
    {
        var cartItem = _cartItems.FirstOrDefault(ci => ci.Product.Id == productId);
        if (cartItem != null)
        {
            if (quantity <= 0)
            {
                RemoveFromCart(productId);
            }
            else
            {
                cartItem.Quantity = quantity;
            }
        }
    }

    public IEnumerable<CartItem> GetCartItems()
    {
        return _cartItems;
    }
}
```

## Step 3: Register Services 
In UnityConfig register the CartItem Services:

```csharp
public static void RegisterServices(IUnityContainer container)
{
    //Add the following at the end of this method
    container.RegisterType<ICartService, CartService>();
}
```

## Step 4: Create a Cart Controller
Create a controller to handle cart actions.

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
        return View(cartItems); // Pass the cart items to the view
    }

    [HttpPost]
    public ActionResult AddToCart(int productId, int quantity)
    {
        _cartService.AddToCart(productId, quantity);
        return RedirectToAction("Index");
    }

    [HttpPost]
    public ActionResult RemoveFromCart(int productId)
    {
        _cartService.RemoveFromCart(productId);
        return RedirectToAction("Index");
    }

    [HttpPost]
    public ActionResult UpdateQuantity(int productId, int quantity)
    {
        _cartService.UpdateQuantity(productId, quantity);
        return RedirectToAction("Index");
    }
}
```

## Step 5: Update the Cart View
Modify your view to add buttons for removing items and adjusting quantities. Here’s an example update for `Views/Cart/Index.cshtml`:

```html

@model IEnumerable<CartItem>

<h2>Your Shopping Cart</h2>

<table>
    <thead>
        <tr>
            <th>Product</th>
            <th>Quantity</th>
            <th>Price</th>
            <th>Actions</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var item in Model)
        {
            <tr>
                <td>@item.Product.Name</td>
                <td>
                    <form action="@Url.Action("UpdateQuantity", "Cart")" method="post" style="display:inline;">
                        <input type="hidden" name="productId" value="@item.Product.Id" />
                        <input type="number" name="quantity" value="@item.Quantity" min="1" />
                        <input type="submit" value="Update" />
                    </form>
                </td>
                <td>@(item.Product.Price * item.Quantity)</td>
                <td>
                    <form action="@Url.Action("RemoveFromCart", "Cart")" method="post" style="display:inline;">
                        <input type="hidden" name="productId" value="@item.Product.Id" />
                        <input type="submit" value="Remove" />
                    </form>
                </td>
            </tr>
        }
    </tbody>
</table>

<p>
    <strong>Total: </strong>
    @Model.Sum(item => item.Product.Price * item.Quantity)
</p>
```

## Display **Cart** button in the navigation

To display a "Cart" button in the navigation that only appears when there are items in the cart, you can follow these steps:

### Step 1: Update the Cart Service
Make sure your CartService has a method to check if the cart has items.

```csharp

public interface ICartService
{
    // Existing methods...
    bool HasItems();
}
```

```c# 
public class CartService : ICartService
{
    // Existing fields and methods...

    public bool HasItems()
    {
        return _cartItems.Any();
    }
}
```
### Step 2: Update the Cart Controller
Update the CartController to include a method that determines if there are items in the cart.

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
        return View(cartItems); // Pass the cart items to the view
    }

    // Existing methods...

    public bool HasItems()
    {
        return _cartService.HasItems();
    }
}
```
### Step 3: Create a ViewModel for Navigation
Create a simple ViewModel to pass the cart status to your layout view.

```csharp
public class NavigationViewModel
{
    public bool CartHasItems { get; set; }
}
```

### Step 4: Update the Layout View
In your layout view (e.g., _Layout.cshtml), you can check if the cart has items and display the button accordingly.

Add a method to fetch cart status in your controller that renders the layout:
```csharp

public class HomeController : Controller
{
    private readonly ICartService _cartService;

    public HomeController(ICartService cartService)
    {
        _cartService = cartService;
    }

    public ActionResult Navigation()
    {
        var model = new NavigationViewModel
        {
            CartHasItems = _cartService.HasItems()
        };

        return PartialView("_Navigation", model);
    }
}
```

- Create a partial view for the navigation (e.g., _Navigation.cshtml):
```html
@model YourNamespace.NavigationViewModel

<nav>
    <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/Products">Products</a></li>
        @if (Model.CartHasItems)
        {
            <li><a href="/Cart">Cart</a></li>
        }
    </ul>
</nav>
```
Render the partial view in your layout:
In your _Layout.cshtml, replace the existing navigation section with the following:

```html
@Html.Action("Navigation", "Home")
```
### Step 5: Update Cart Service Registration
Ensure that your CartService is registered correctly in the Unity container so it can be resolved in the HomeController.