# Lab 1 Build Guide

This guide is the short version of the Lab 1 Instructions. It covers only **how to build your
feature**. The full Lab 1 Instructions still cover the stories, teamwork, grading, and what you
hand in. If the two documents ever disagree, the Lab 1 Instructions are correct.

## 1. Before you start {.section}

1. Accept Lab 1 in GitHub Classroom, clone your team's repository, and open the solution.
2. Run the tests in Test Explorer. Your feature's tests fail for now. That is expected: each failure
   names the type it is looking for.
3. Create a branch for your feature. Merge your work to `main` through pull requests.
4. In `Program.cs`, uncomment **your own** service line, and only that line.
5. Do not edit `Home.razor`, `NavMenu.razor`, or `MainLayout.razor`. They belong to the whole team.

**Names come from your contract table.** The tests call your code by exact name: the namespace,
the type name, the member name, and the parameter types and order. Parameter names are yours.

| Feature folder | Type names start with | Namespace pattern |
|---|---|---|
| `ProductCatalog` | `Product` | `InventoryBrowser.Features.ProductCatalog.Store` |
| `ShoppingCart` | `Cart` | `InventoryBrowser.Features.ShoppingCart.Store` |
| `Wishlist` | `Wishlist` | `InventoryBrowser.Features.Wishlist.Store` |
| `StockAlerts` | `Alert` | `InventoryBrowser.Features.StockAlerts.Store` |
| `CategoryManager` | `Category` | `InventoryBrowser.Features.CategoryManager.Store` |

Guards go in `<Feature>.Guards`, services in `<Feature>.Services`. **Create the folder first, then
add the class**, so Visual Studio sets the right namespace.

## 2. The pattern every feature follows {.section}

| Piece | What you write | Rule | Learned on |
|---|---|---|---|
| State | A `record` with `init` properties | Never change state directly. Reducers return `state with { ... }` | Day 4 |
| Actions | One `record` per action | Your table lists only the actions the tests call. Your feature needs more (see your feature below) | Day 4 |
| Reducers | A `static class` with one method per action | Each reducer is `(State state, Action action)` and returns a new state | Day 4 |
| Guard | A `static` method that returns `bool` | `true` means "redirect". Your detail page calls it **before** anything else | Day 7 |
| Service | A class that implements your feature's interface | **Every** method returns `Result<T>`: `WithValue(...)` on success, `AddError(...)` on failure | Day 6 |
| Service effect | An `[EffectMethod]` that calls your service | See below | Day 5 |
| Persistence effect | An `[EffectMethod]` that saves and loads with local storage | See below | Day 8 |
| Pages | Two `.razor` pages with route constraints | Each `@code` block stays under 20 lines. No business logic in `.razor` files | Days 1, 2, 9 |
| Tests | In `InventoryBrowser.StudentTests` | 3+ reducer tests, and 1 guard test for the case where the guard does **not** redirect | Days 4, 7 |

**Every effect uses three actions.** One starts it, one reports success, and one reports failure.
For example: `LoadProductsAction` → `LoadProductsSuccessAction` or `LoadProductsFailureAction`.

1. Your page (or another effect) dispatches the start action.
2. Your effect waits with `await Task.Delay(300);`, then calls your service.
3. If the `Result<T>` is a success, dispatch the success action. Otherwise, dispatch the failure action.
4. The success action and the failure action each need their own reducer.

**A failure reducer needs something to change.** Add a `string? ErrorMessage` property to your
state. Set it in the failure reducer, clear it in the success reducer, and show it on your page.
You may add properties like this one. The tests only set the properties in your table.

**Saving and restoring uses two actions.**

1. After every change, your persistence effect saves your **collection** (not the whole state) to
   local storage, under a key that names your feature, for example `"inventory.wishlist"`.
2. After your page's first render, the page dispatches a request action, for example
   `HydrateWishlistRequestAction`.
3. Your persistence effect reads local storage. If it finds a saved list, it dispatches the
   `Hydrate<X>Action` from your table.

**Build in this order:** folders → state → all your action records → the reducers in your table
(run your tests) → your own reducers → guard (run your tests) → service, then uncomment your
`Program.cs` line → service effect → list page → detail page → persistence effect → your own tests.

**Three page mistakes to avoid:**

- **Restore in `OnAfterRenderAsync`, not `OnInitialized`.** Dispatch your hydrate request inside
  `if (firstRender)` in `OnAfterRenderAsync`. The page is first built on the server, where there is
  no browser, so reading local storage in `OnInitialized` throws an error.
- **`return` after you redirect.** On your detail page, call the guard first. If it says redirect,
  call `NavigateTo(...)` and then `return`, so the page does not go on to dispatch your select
  action for an Id that does not exist.
- **Do not add `@rendermode` to your pages.** The starter already sets it once for the whole app,
  in `App.razor`.

## 3. Your feature {.section}

**Jump to your feature:** [Product Catalog](#product-catalog) · [Shopping Cart](#shopping-cart) · [Wishlist](#wishlist) · [Stock Alerts](#stock-alerts) · [Category Manager](#category-manager)

Find your feature below. **Fixed** names are called by the tests: copy them exactly. **Yours**
names are suggestions: you may rename them.

### Product Catalog {.feature}

**Pages:** `/products` (list, with a category filter) and `/products/{Id:int}` (detail).
**State:** `ProductState` with `Items`, `SelectedProduct`, `IsLoading`, `FilterCategoryId`.
`Items` starts from `InventorySeedData.Products`.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `LoadProductsAction` | Yours | List page, when it opens | Sets `IsLoading` to `true`. Your service effect runs on it |
| `LoadProductsSuccessAction(IReadOnlyList<Product> Products)` | Yours | Service effect, when `GetAllProducts` succeeds | Replaces `Items`, sets `IsLoading` to `false` |
| `LoadProductsFailureAction(string Message)` | Yours | Service effect, when the service fails | Sets `IsLoading` to `false`, stores the message |
| `FilterByCategoryAction(int? CategoryId)` | **Fixed** | List page, when a category is picked (`null` = all) | Sets `FilterCategoryId` |
| `SelectProductAction(int ProductId)` | **Fixed** | Detail page, after the guard passes | Sets `SelectedProduct` to the item with that Id, or `null` |
| `HydrateProductRequestAction` | Yours | List page, after its first render | None. The persistence effect runs on it |
| `HydrateProductAction(IReadOnlyList<Product> Items)` | **Fixed** | Persistence effect | Replaces `Items` |

**Guard:** `ProductGuard.ShouldRedirect(ProductState state, int productId)` is `true` when no item
in `Items` has that Id. Redirect to `/products`.
**Service:** `ProductService`. `GetProductById` fails with error code `NotFound` and a message that
includes the Id. `GetAllProducts` always succeeds.
**Also:** the detail page has **Add to cart** and **Add to wishlist** buttons. They dispatch
`AddToCartAction` and `AddToWishlistAction`, which are already in the starter.

**Easy to mix up:**

- `FilterByCategoryAction` does not remove anything from `Items`. It only sets `FilterCategoryId`,
  and the list page uses it to choose what to show.
- `LoadProductsSuccessAction` and `HydrateProductAction` both replace `Items`. One brings the list
  from your service, the other from local storage.

### Shopping Cart {.feature}

**Pages:** `/cart` (catalogue with **Add to cart** buttons, cart items, total) and `/cart/checkout`
(summary).
**State:** `CartState` with `Items`, `Total`, `ItemCount`. Starts empty.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `AddToCartAction(int ProductId, string Name, decimal Price)` | **Fixed** (in the starter) | Cart page. Also Product Catalog and Wishlist | Adds with quantity 1, or adds 1 to the quantity |
| `UpdateQuantityAction(int ProductId, int NewQuantity)` | **Fixed** | Cart page, when a quantity changes | Sets the quantity. 0 or less removes the item |
| `RemoveFromCartAction(int ProductId)` | **Fixed** | Cart page **Remove** | Removes the item |
| `CalculateTotalAction` | Yours | Your effect, after a cart change | None. Your service effect runs on it |
| `CalculateTotalSuccessAction(decimal Total)` | Yours | Service effect, when `CalculateCartTotal` succeeds | Sets `Total` |
| `CalculateTotalFailureAction(string Message)` | Yours | Service effect, when the service fails | Stores the message |
| `HydrateCartRequestAction` | Yours | Cart page, after its first render | None. The persistence effect runs on it |
| `HydrateCartAction(IReadOnlyList<CartItem> Items)` | **Fixed** | Persistence effect | Replaces `Items` |

**After every change** to `Items`, the reducer recalculates `Total` (price × quantity, summed) and
`ItemCount` (quantities, summed). Write that calculation once, in a helper.
**Guard:** `CartGuard.ShouldRedirectFromCheckout(CartState state)` is `true` when the cart is empty.
It has no Id. Redirect to `/cart`.
**Service:** `CartService.CalculateCartTotal`. An empty cart succeeds with 0. A quantity below 0
fails with error code `CartService.NegativeQuantity`.

**Easy to mix up:**

- Add **adds 1** to the quantity. Update **sets** the quantity.
- Remove and Update with 0 both take the item out. Remove is for the button, and Update with 0 is
  what happens when the user types 0.
- Your reducers and your service effect both produce `Total`. The two values should always match.

### Wishlist {.feature}

**Pages:** `/wishlist` (catalogue with **Add** buttons, saved items) and `/wishlist/{Id:int}`
(detail).
**State:** `WishlistState` with `Items` and `SelectedItem`. Starts empty.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `AddToWishlistAction(Product Product)` | **Fixed** (in the starter) | Wishlist page. Also Product Catalog | Adds the product, unless it is already there |
| `RemoveFromWishlistAction(int ProductId)` | **Fixed** | Wishlist page **Remove** | Removes the product |
| `MoveToCartAction(int ProductId)` | **Fixed** | Wishlist page **Move to cart** | Removes the product. Your effect then dispatches `AddToCartAction` |
| `MoveToCartFailureAction(string Message)` | Yours | Move-to-cart effect, when the product is not in `InventorySeedData.Products` | Stores the message |
| `SelectWishlistItemAction(int ProductId)` | Yours | Detail page, after the guard passes | None. Your service effect runs on it |
| `SelectWishlistItemSuccessAction(Product Item)` | Yours | Service effect, when `GetWishlistItem` succeeds | Sets `SelectedItem` |
| `SelectWishlistItemFailureAction(string Message)` | Yours | Service effect, when the service fails | Stores the message |
| `HydrateWishlistRequestAction` | Yours | Wishlist page, after its first render | None. The persistence effect runs on it |
| `HydrateWishlistAction(IReadOnlyList<Product> Items)` | **Fixed** | Persistence effect | Replaces `Items` |

**Guard:** `WishlistGuard.ShouldRedirect(WishlistState state, int id)` is `true` when no item in
`Items` has that Id, including when the wishlist is empty. Redirect to `/wishlist`.
**Service:** `WishlistService.GetWishlistItem(IReadOnlyList<Product> wishlistItems, int productId)`
fails with error code `NotFound` and a message that includes the Id.

**Easy to mix up:**

- Remove and Move to cart both take the product off the wishlist. Move to cart also puts it in the
  cart.
- `AddToWishlistAction` is yours to handle. `AddToCartAction` belongs to Shopping Cart: you only
  dispatch it.

### Stock Alerts {.feature}

**Pages:** `/alerts` (create form, alerts with low or OK status, triggered count) and
`/alerts/{ProductId:int}` (detail).
**Model:** `record StockAlert(int ProductId, string ProductName, int ThresholdQuantity, bool IsTriggered)`.
**State:** `AlertState` with `Alerts` and `ActiveCount`. Starts empty.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `CreateAlertAction(int ProductId, string ProductName, int ThresholdQuantity)` | **Fixed** | Alerts page form | Adds the alert with `IsTriggered = false`, unless the product already has one. Your effect then dispatches `CheckStockAction` |
| `DeleteAlertAction(int ProductId)` | **Fixed** | Alerts page **Delete** | Removes the alert |
| `CheckStockAction(int ProductId, int ThresholdQuantity)` | Yours | Your effect, after an alert is created | None. Your service effect runs on it |
| `CheckStockSuccessAction(int ProductId, bool IsTriggered)` | **Fixed** | Service effect, when `CheckStock` succeeds. `IsTriggered` = stock at or below the threshold | Sets `IsTriggered` on that alert |
| `CheckStockFailureAction(int ProductId, string Message)` | Yours | Service effect, when the service fails | Stores the message |
| `HydrateAlertRequestAction` | Yours | Alerts page, after its first render | None. The persistence effect runs on it |
| `HydrateAlertAction(IReadOnlyList<StockAlert> Alerts)` | **Fixed** | Persistence effect | Replaces `Alerts` |

**After every change**, the reducer sets `ActiveCount` to the number of triggered alerts. Write that
calculation once, in a helper.
**Guard:** `AlertGuard.ShouldRedirect(AlertState state, int productId)` is `true` when that product
has no alert. Redirect to `/alerts`.
**Service:** `AlertService.CheckStock(int productId)` succeeds with the stock quantity (0 is still a
success). An unknown product fails with error code `NotFound` and a message that includes the Id.

**Easy to mix up:**

- Create **adds** the alert, always with `IsTriggered = false`. The stock check **finds out**
  whether stock is low.
- `CheckStockAction` **asks** for a check. `CheckStockSuccessAction` **carries the answer** back.

### Category Manager {.feature}

**Pages:** `/categories` (list) and `/categories/{Id:int}` (one category and its products).
**State:** `CategoryState` with `Categories`, `SelectedCategory`, `FilteredProducts`. `Categories`
starts from `InventorySeedData.Categories`.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `LoadCategoriesAction` | Yours | List page, when it opens | None. Your load effect runs on it |
| `LoadCategoriesSuccessAction(IReadOnlyList<Category> Categories)` | **Fixed** | Load effect, with `InventorySeedData.Categories` | Replaces `Categories` |
| `LoadCategoriesFailureAction(string Message)` | Yours | Load effect, when there are no categories | Stores the message |
| `SelectCategoryAction(int CategoryId)` | **Fixed** | Detail page, after the guard passes | Sets `SelectedCategory` to the category with that Id, or `null`. Your service effect also runs on it |
| `FilterProductsAction(int CategoryId)` | **Fixed** | Service effect, when `GetCategoryById` succeeds | Sets `FilteredProducts` to the products in `InventorySeedData.Products` with that `CategoryId` |
| `SelectCategoryFailureAction(string Message)` | Yours | Service effect, when the service fails | Stores the message |
| `HydrateCategoryRequestAction` | Yours | List page, after its first render | None. The persistence effect runs on it |
| `HydrateCategoryAction(IReadOnlyList<Category> Categories)` | **Fixed** | Persistence effect | Replaces `Categories` |

**Guard:** `CategoryGuard.ShouldRedirect(CategoryState state, int categoryId)` is `true` when no
category in `Categories` has that Id. Redirect to `/categories`.
**Service:** `CategoryService.GetCategoryById(int id)` fails with error code `NotFound` and a
message that includes the Id. It is the only service method, so your load effect reads
`InventorySeedData.Categories` directly, and your **service effect** is the one on
`SelectCategoryAction`.

**Easy to mix up:**

- `SelectCategoryAction` finds the **category** (name and description). `FilterProductsAction`
  finds the **products** in it. The detail page needs both, for the same Id.
- `LoadCategoriesSuccessAction` and `HydrateCategoryAction` both replace `Categories`. One brings
  the list from the seed data, the other from local storage.

**Why it works this way:** Category Manager is the one feature whose data the user cannot
change, so some requirements look odd. They are deliberate:

- **You save and restore `Categories`, even though the list never changes.** Every feature
  practises the same save-and-restore pattern, and it is graded the same way. A refresh looks the
  same with or without it, so check it in the browser instead.
  To check that your save works:

  1. Run the app and open `/categories`.
  2. Press **F12** to open the developer tools.
  3. Click the **Application** tab. If you do not see it, click **»** to find it.
  4. In the left panel, expand **Local storage** and click your app's address (for example
     `https://localhost:7123`).
  5. Find your key in the list, for example `inventory.category-manager`. Its value is your saved
     categories, as JSON text. If the key is there, your save works.
- **Your load effect's failure branch never runs**, because the seed data always has five
  categories. Write it anyway: every effect handles both outcomes.
- **Your service effect runs on `SelectCategoryAction`**, because `GetCategoryById` is the only
  service method. An unknown Id is where a real failure happens.
- **Select and Filter are separate** so each reducer can be tested on its own.

## 4. Before you hand in {.section}

- [ ] Your feature builds with no warnings.
- [ ] All of your feature's visible tests pass in Test Explorer.
- [ ] Every rule in your contract table's **Behaviour** column works, not only the ones the visible
  tests check. The hidden tests check those edge cases, for example adding a product that is
  already there, or asking for an Id that does not exist.
- [ ] Your own 3+ reducer tests and 1 guard test pass.
- [ ] A wrong-type URL (for example `/products/abc`) does not crash or show a blank page.
- [ ] An unknown Id on your detail page sends you back to your list page.
- [ ] Your feature's data is still there after a hard refresh (Ctrl+Shift+R).
- [ ] Your work is merged to `main` through reviewed pull requests.
- [ ] Your feature README (`Features/<YourFeature>/README.md`) answers its four headings.

The walkthrough recording and peer evaluation are covered in **What You Hand In** in the Lab 1
Instructions.
