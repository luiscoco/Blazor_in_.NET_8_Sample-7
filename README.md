# Blazor in .NET 8: Sample 7

Let's dive into Blazor's state management features, focusing on ways to share data and manage updates across components

**Example: Shared Shopping Cart**

**Features Demonstrated**:

Cascading Parameters: Passing data down a component hierarchy.

State Management Services: Creating a centralized service to hold application state.

Event Handling: Reacting to changes and updating the UI across components.

**Setup**

**Project**: Create a new Blazor Server project (or reuse an existing one). You could name it "ShoppingCartApp"

**Models**

ShoppingCartItem.cs (Inside a Models folder):

C#
namespace ShoppingCartApp.Models;

public class ShoppingCartItem
{
    public string ProductName { get; set; }
    public decimal Price { get; set; }
    public int Quantity { get; set; }
}
Usa el código con precaución.
Services

ShoppingCartService.cs (Create a Services folder):

C#
namespace ShoppingCartApp.Services;

public class ShoppingCartService
{
    public event Action OnChange;  // Event for change notifications

    private List<ShoppingCartItem> items = new List<ShoppingCartItem>();

    public List<ShoppingCartItem> GetItems() => items.ToList(); // Read-only view

    public void AddItem(ShoppingCartItem item)
    {
        items.Add(item);
        OnChange?.Invoke(); 
    }

    // ... (potentially more methods like RemoveItem, UpdateQuantity, etc.)
}
Usa el código con precaución.
Components

ShoppingCart.razor  (Inside Shared folder):

Razor CSHTML
@inject ShoppingCartService CartService
@implements IDisposable 

<h3>Shopping Cart</h3>

<ul>
    @foreach (var item in CartService.GetItems())
    {
        <li>@item.ProductName - @item.Price ($@item.Quantity)</li>
    }
</ul>

@code {
    protected override void OnInitialized()
    {
        CartService.OnChange += StateHasChanged; // Subscribe to changes
    }

    public void Dispose()
    {
        CartService.OnChange -= StateHasChanged; // Unsubscribe
    }
}
Usa el código con precaución.
ProductDisplay.razor  (Inside Pages folder):

Razor CSHTML
@page "/product-display"
@inject ShoppingCartService CartService

<CascadingValue Value="this"> 
    <h1>Products</h1>
    @* Display products and have "Add to Cart" buttons that call CartService.AddItem *@
</CascadingValue>
Usa el código con precaución.
In your App.razor:

Razor CSHTML
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code{
    protected override void OnInitialized()
    {
        // Register the ShoppingCartService as a scoped service
        builder.Services.AddScoped<ShoppingCartService>();
    }
}
Usa el código con precaución.



