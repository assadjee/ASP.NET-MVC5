# SportsStore Application

## Tasks
- Complete the pending tasks for **Week 3** Day 4 and Day 5.
- Apply bootstrap css styles on all the views and DataTables
- Proper labeling and titles on all pages
- Navbar update with the proper links


### Sample for Updating Bootstrap styles for buttons and links

```html
<p>
    @Html.ActionLink("Add New Product", "Create",null, new {@class="btn btn-success"})
</p>
<div>

@Html.ActionLink("Edit", "Edit", new { id=item.Id }, new { @class = "btn btn-primary" }) 
@Html.ActionLink("Details", "Details", new { id=item.Id }, new {@class = "btn btn-secondary"}) 
@Html.ActionLink("Delete", "Delete", new { id=item.Id }, new {@class = "btn btn-danger"})
</div>
```

#### Notes for Reference
- Updated Order DataTable
- Fix Shopping Cart to get items from database (Fixed). Check [here](/Week%204/Day%202/CartItem.md)
- Shared Layout: Show Order for Admin. Get the [Details](/Week%204/Day%202/TopNavigation.md).  
- Show Cart for Customers only and only when there is an item in the cart. (Fixed) For details [follow](/Week%204/Day%202/CartItem.md) here.
- Identity Register Error with UnityConfig (Fixed). Check this [link](/Week%204/Day%202/LoginAndIdentityFix.md)