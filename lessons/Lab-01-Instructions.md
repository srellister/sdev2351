# Lab 1: Team Navigation & State Application

**Weight:** 30% of Final Grade
**Module:** 1 -- Routing & State Management (Weeks 1-4)
**Assigned:** Day 9 (full spec + rubric + starter code)
**Due:** see the Course Schedule | **In-Class Lab Days:** 3 (Days 11, 15, 18)
**COs:** CO1 (Routing), CO2 (State Management), CO6 (Team Collaboration -- Forming)
**Teams:** 3-5 students (new teams formed for this lab)

<!-- GITHUB-CLASSROOM-CALLOUT: the per-section invite link lives on the Lab 1 page (a module description), where each instructor pastes it. This document deliberately carries no link, so a Manage Files upload can never overwrite an instructor's link. -->
<div style="border:2px solid #2E7D32; border-radius:8px; background:#F1F8E9; padding:16px 20px; margin:20px 0;">
  <div style="font-size:1.05rem; font-weight:700; color:#1B5E20; margin-bottom:8px;"><span style="display:inline-block; width:22px;">&#9095;</span>Do your work in GitHub Classroom</div>
  <p style="margin:0; font-size:0.95rem; color:#333;">Your code goes in your team's <strong>GitHub Classroom</strong> repository. <strong>Your walkthrough recording and your peer evaluation are uploaded to the Group Lab 1 folder in Brightspace.</strong> The full list is in <a href="#what-you-hand-in" style="color:#1B5E20;"><strong>What You Hand In</strong></a>, near the end of this document. To create your team repository, open the <strong>Lab 1</strong> page in Brightspace and click <strong>Accept Lab 1 in GitHub Classroom</strong>. Your instructor adds that link for your section.</p>
</div>

### Timeline

| Day | Event |
|-----|-------|
| **Day 9** | **Formal Assignment** -- Full spec, rubric, and starter code released |
| **Day 10** | Review + Team Formation + Architecture Planning |
| **Day 11** | In-Class Lab Day 1 -- Team Kickoff + Initial Architecture |
| **Day 15** | In-Class Lab Day 2 -- Mid-Point Check-in (instructor feedback on progress) |
| **Day 18** | In-Class Lab Day 3 -- Final Work Session. **Lab 1 due: see the Course Schedule** -- code, walkthrough recording, and peer evaluation (both uploaded to Brightspace) |

---

## Overview

Working in a team, you will build an **Inventory Browser** application using Blazor and Fluxor. Each student builds a **complete feature module** that exercises all of the core course outcomes for this lab.

Every student implements their own routing, Fluxor state management, route guards, BYSResult service pattern, and unit tests -- so every team member demonstrates mastery of both CO1 and CO2. Features share data through Fluxor's centralized store and can interact, but no feature depends on another to function.

This lab assesses your ability to implement dynamic routing patterns and centralized state management while collaborating through Git workflows.

![Lab 1 Fluxor Unidirectional Data Flow — Component dispatches Action → Store routes to Reducer (sync) or Effect (async) → new State → Component re-renders](../assets/lab-01-fluxor-flow.svg)

The diagram above shows the single-direction Fluxor cycle your feature will implement. Every data change in your feature module must follow this path: components **dispatch** actions, the store routes each action to a **reducer** (synchronous, pure) or an **effect** (asynchronous, side-effecting), new **state** is produced, and subscribed components re-render. No component ever writes to state directly — this rule is what makes the flow testable and predictable.

---

## Course Material — Where You Learned This

Everything Lab 1 assesses was taught in Module 1. Keep these open as your reference while you build:

- **Routing (CO1):** **Day 1 — Environment Setup & Nested Routes** · **Day 2 — Programmatic Navigation** · **Day 7 — Route Guards & Navigation State** · **Day 9 — Advanced Routing Patterns**
- **State management (CO2):** **Day 3 — Flux/Redux Pattern Intro** · **Day 4 — Fluxor: Actions & Reducers** · **Day 5 — Fluxor: Effects & Async** · **Day 6 — Centralized State & Result Pattern**

---

## How It Works: Vertical Slice Architecture

Instead of dividing the work by technical layer (one student does routing, another does state), each student builds a **self-contained feature through every layer, from the pages down to the state and services**. Think of each feature as a vertical slice through the application -- it has its own pages, its own state, its own route guards, and its own tests.

**Why this approach?**
- Every student demonstrates every CO being assessed
- Features are independent -- if one student falls behind, the other students are not blocked
- Code reviews are more useful because students compare similar implementations
- Git history clearly shows who built what

---

## The Inventory Browser

All teams build the same application domain: an **Inventory Browser**. This provides a consistent starter code base, a single instructor test suite, and natural patterns for routing, state management, and guards.

The application has 5 feature modules. Each student owns one feature end-to-end.

### Feature Modules

| Feature | Pages | State Slice | Route Guard | Key Actions |
|---------|-------|-------------|-------------|-------------|
| [**Product Catalog**](#contract-product-catalog) | `/products`, `/products/{id:int}` | ProductState (items, selectedProduct, isLoading) | Detail page redirects if product not found | LoadProducts, SelectProduct, FilterByCategory |
| [**Shopping Cart**](#contract-shopping-cart) | `/cart`, `/cart/checkout` | CartState (items, total, itemCount) | Checkout redirects if cart is empty | AddToCart, RemoveFromCart, UpdateQuantity |
| [**Wishlist**](#contract-wishlist) | `/wishlist`, `/wishlist/{id:int}` | WishlistState (items, selectedItem) | Detail redirects if that item is not in the wishlist | AddToWishlist, RemoveFromWishlist, MoveToCart |
| [**Stock Alerts**](#contract-stock-alerts) | `/alerts`, `/alerts/{productId:int}` | AlertState (alerts, activeCount) | Alert detail redirects if alert does not exist | CreateAlert, DeleteAlert, CheckStock |
| [**Category Manager**](#contract-category-manager) | `/categories`, `/categories/{id:int}` | CategoryState (categories, selectedCategory) | Category detail redirects if category not found | LoadCategories, SelectCategory, FilterProducts |

### Target sketches — what each feature looks like

Each feature below links to a quick, **rough hand-drawn sketch** of the two pages you build. The sketches show the general *shape* of the target, and the routing, state, and route-guard pieces behind each screen. They are **not** a design to copy. Your layout will look different, and that is expected: the lab grades your routing, state management, and guard patterns, not the exact visual design.

| Feature | Page sketches |
|---------|---------------|
| **Product Catalog** | [List — `/products`](../assets/lab-01-product-catalog-list.png) &middot; [Detail — `/products/{id}`](../assets/lab-01-product-catalog-detail.png) |
| **Shopping Cart** | [Cart — `/cart`](../assets/lab-01-shopping-cart-cart.png) &middot; [Checkout — `/cart/checkout`](../assets/lab-01-shopping-cart-checkout.png) |
| **Wishlist** | [List — `/wishlist`](../assets/lab-01-wishlist-list.png) &middot; [Detail — `/wishlist/{id}`](../assets/lab-01-wishlist-detail.png) |
| **Stock Alerts** | [List — `/alerts`](../assets/lab-01-stock-alerts-list.png) &middot; [Detail — `/alerts/{productId}`](../assets/lab-01-stock-alerts-detail.png) |
| **Category Manager** | [List — `/categories`](../assets/lab-01-category-manager-list.png) &middot; [Detail — `/categories/{id}`](../assets/lab-01-category-manager-detail.png) |

---

## Deliverables

### Team Deliverables
1. **Blazor application** with the shared navigation shell and one feature module per student
2. **Git repository** with evidence of team collaboration (branches, PRs, commits from all members)
3. **Team `README.md`** in the repository root: the starter already has the setup steps. Each
   student fills in their own row of the **Team Members** table. The team fills in **Beyond the
   Requirements**.

### Per-Student Deliverables (within the team repo)
Each student builds **one feature module** that includes:

| Requirement | Details |
|-------------|---------|
| **Routable Pages** | 2 pages with route parameters and at least one route constraint |
| **Fluxor State Slice** | Feature state record, your contract actions and reducers, and one effect that calls your service and dispatches a success action or a failure action |
| **Route Guard** | The guard named in your contract table, called from your page |
| **BYSResult Pattern** | **Every** method in your service returns `Result<T>` |
| **Unit Tests** | 3+ reducer tests + 1 guard test for the case where the guard does **not** redirect |
| **Feature README** | `Features/<YourFeature>/README.md` -- your architecture notes (see **Your Feature README** below) |
| **Walkthrough recording** | Uploaded to the **Group Lab 1** folder in Brightspace -- see **What You Hand In** |

---

## Feature Module Requirements

Each feature module must include all of the following:

### Routing (CO1)
- At least 2 routable pages within the feature area
- Route parameters with appropriate constraints (e.g., `{id:int}`)
- A URL with the wrong type (for example `/products/abc`) is rejected cleanly: it must not throw an
  unhandled exception or leave a blank page
- Route URLs follow REST conventions: plural resource names, the Id in the path, and no verbs
  (`/products/{id:int}`, not `/get-product/{id}`)
- At least one route guard that prevents navigation based on state (e.g., redirect from detail page when item not found)

### State Management (CO2)
- A dedicated Fluxor feature state record (e.g., `ProductState`, `CartState`)
- At least 3 actions with corresponding reducers
- **One effect that calls your service.** Mark it `[EffectMethod]`, inject your service through
  the constructor, and make the method `async`. Your service methods are synchronous, so simulate
  the network call first (for example `await Task.Delay(300);`, as on Day 5). Then call the service
  and dispatch a **success** action or a **failure** action, depending on the `Result<T>`. Each of
  those two actions needs its own reducer. This is the full A4 flow in the rubric. More effects are
  welcome but earn no extra points.

### Service Layer
- **Every** service method returns `Result<T>` (the BYSResult pattern): `WithValue(...)` on
  success, `AddError(...)` on failure. Do not wrap a whole method body in a `try/catch` that turns
  every exception into an error.
- Clean separation between UI components and business logic: no business logic in `.razor`
  files, and each `@code` block stays **under 20 lines**. Move longer logic into your service,
  your reducers, or your effect.

### Testing
- **3+ reducer tests** -- verify state transitions using the AAA (Arrange-Act-Assert) pattern
- **1 guard test for the case where the guard does NOT redirect.** The visible instructor test
  already covers the redirect case, so a copy of it earns no marks. The StudentTests README in the
  starter shows how.
- All tests for **your** feature pass. Tests for a feature that nobody on your team owns stay red,
  and that is expected (see **Team Size Scaling**).
- Tests must have descriptive names following `MethodName_Scenario_ExpectedResult` convention

> **Note:** Route configuration tests (testing that routes are wired correctly via reflection/bUnit) are **not required**. Focus your testing effort on reducers and guard logic, which are pure C# functions.

> **Auto-tested (you must implement this, but you do not write tests for it):** The instructor test suite verifies your BYSResult service layer automatically. You **must implement the BYSResult pattern correctly** in your service code -- the auto-tests call your service methods and check that success and failure paths return the correct `Result<T>` values. You do not need to write your own BYSResult tests, but your service implementation must work.

### State Persistence (Required -- every student)

Your feature's state must survive a **browser refresh**. If the user closes the tab and reopens the app,
what the user added to your feature must still be there.

- **Each student persists their own feature's state.** This is not a team-level goal, and it is not only
  for one chosen feature -- all five feature modules must persist their state.
- **Persist the collection**, not the whole state record. Restoring a stale (out-of-date) `SelectedItem` selects
  something the user never picked.
- **Namespace the storage key to your feature.** If five students share one key, they overwrite each
  other's data.
- **Restore from your own page, not from a shared layout file.** The Day 8 cart demo restores in
  `MainLayout` because a cart badge is global chrome (part of the layout shown on every page). Your
  feature is not global chrome. If five students all edit `MainLayout`, they create a merge conflict,
  and only one student's version can be kept as written. Dispatch from your own page.
- **Your `Hydrate<X>Action` record and its reducer are in your feature's contract table, and
  those names are fixed** -- the instructor tests call them by name (see Feature Contracts
  below). Your work is to write the reducer, and the effect that saves and loads the state.
- **This is scored under A9 (Immutable State)** in the rubric, plus a hidden reducer test. There is no
  separate rubric row and no bonus for it.

Day 8 covers browser storage.

---

## Feature Contracts

Each feature starts with its **story**: what the business needs, who uses each page, and what they
can do there. Read it first. It tells you what you are building and why. The **contract** under it
is the build-to-spec part: the exact names the instructor tests use. For the full checklist, see
**Acceptance Criteria by Feature** below.

**Jump to your feature:** [Product Catalog](#contract-product-catalog) · [Shopping Cart](#contract-shopping-cart) · [Wishlist](#contract-wishlist) · [Stock Alerts](#contract-stock-alerts) · [Category Manager](#contract-category-manager)

The instructor tests call your code **by exact name**. Your feature's table below is the list of
names the tests use. Match every one exactly: the namespace, the type name, the member name, and
the parameter **types and order**.

**Type names come from your table, not from your folder name.** In four of the five features, the
folder (and its namespace) uses a different word from the type names. Copy each name from your
table. Do not build it from the folder name.

| Feature folder and namespace | Type names start with | Example |
|---|---|---|
| `ShoppingCart` | `Cart` | `CartState`, `CartReducers`, `CartGuard` |
| `ProductCatalog` | `Product` | `ProductState`, `ProductReducers`, `ProductGuard` |
| `StockAlerts` | `Alert` | `AlertState`, `AlertReducers`, `AlertGuard` |
| `CategoryManager` | `Category` | `CategoryState`, `CategoryReducers`, `CategoryGuard` |
| `Wishlist` | `Wishlist` | `WishlistState`, `WishlistReducers`, `WishlistGuard` |

**Create the `Store` and `Guards` folders if your feature does not have them.** The starter has a
`Store` folder only for Shopping Cart and Wishlist, and no feature has a `Guards` folder. The tests
look for each type in the namespace shown in brackets in the Piece column of your table, for
example `InventoryBrowser.Features.ShoppingCart.Store.CartState`. When you add a class to a folder,
Visual Studio sets the namespace from the folder name. So create the folder first, then add the
class.

- **Parameter names are yours.** The tests pass arguments by position, so `int id` and
  `int productId` both work.
- **Your table lists only the names the tests call. Your feature needs more actions than that.**
  Every feature also needs an action that starts its effect, a failure action for that effect,
  and an action that asks for its saved data. Each feature section below has an
  **Every action in this feature** table that lists all of them, in the order they happen. Names
  marked **Yours** are suggestions. You may rename them, because no test calls them.
- **Every effect has three actions:** one that starts it (`LoadProductsAction`), one for success
  (`LoadProductsSuccessAction`), and one for failure (`LoadProductsFailureAction`). Each success
  and failure action needs its own reducer (see **State Management**).
- **A failure reducer needs something to change.** Add a `string? ErrorMessage` property to your
  state record. Set it in the failure reducer, clear it in the success reducer, and show it on your
  page. You may add properties like this one: the tests set only the properties in your table.
- **Restoring saved data takes two actions.** Your page dispatches a request action (for example
  `HydrateProductRequestAction`) after its first render. Your persistence effect reads local
  storage and dispatches the `Hydrate<X>Action` from your table with what it found.
- **Everything else is yours to design:** extra actions, helper methods, page markup, and anything
  else your feature needs.
- **Every service failure** adds an `Error` to the `Result<T>`. A `Result<T>` with no error counts
  as a success.
- **Two actions already exist in the starter.** `AddToCartAction` (Shopping Cart) and
  `AddToWishlistAction` (Wishlist) are dispatched by other features' code, so they are already
  included in the starter code. Product Catalog's detail page dispatches both, and Wishlist's
  move-to-cart effect dispatches `AddToCartAction`. Do not rename them: a new name stops those
  features from compiling. The Shopping Cart and Wishlist students write the reducers that
  handle them.
- **The service interfaces already exist** in each feature's `Services/` folder. Write the class
  that implements your feature's interface, then uncomment **your** line in `Program.cs`.

> **The solution always compiles, and your tests always run, no matter how much of their work your
> teammates have finished.** The instructor tests find your types by name while the tests run. The
> test code does not refer to your types directly. So until you create a type, the tests that need it
> **fail** with a message that names exactly what is missing, for example: *"Contract: type
> 'InventoryBrowser.Features.Wishlist.Guards.WishlistGuard' was not found."* Create what the
> message names, run the tests again, and repeat. If a teammate has not finished their feature, only
> **their** tests fail. Their unfinished feature never stops your tests from running.

Namespaces follow one pattern: `InventoryBrowser.Features.<Feature>.<Folder>`. For example, the
Wishlist guard lives in `InventoryBrowser.Features.Wishlist.Guards`.

### Product Catalog {#contract-product-catalog .feature}

#### The business need {.part}

Shoppers come to the store to find products. The store needs a catalogue they can browse and
filter by category, and a page with the full details of one product, so a shopper can decide
whether to buy it.

#### List page — `/products` {.part}

As a shopper, I want to see the products the store sells, so that I can find something I want to buy.

- I see a list of products.
- I can filter the list by category, so that I see only the products I care about.
- I can open any product to see its details.

#### Detail page — `/products/{id}` {.part}

As a shopper, I want to see everything about one product, so that I can decide whether to buy it.

- I see the product's name, description, price, and how many are in stock.
- I can add the product to my cart or to my wishlist from this page.
- If I open a product that does not exist, I am sent back to `/products`.

#### Product Catalog contract (build to spec) {.part}

| Piece | Exact contract | Behaviour |
|---|---|---|
| State (`Store`) | `record ProductState` with `IReadOnlyList<Product> Items`, `Product? SelectedProduct`, `bool IsLoading`, `int? FilterCategoryId` | The initial state loads `Items` from `InventorySeedData.Products` |
| Actions (`Store`) | `SelectProductAction(int ProductId)`, `FilterByCategoryAction(int? CategoryId)`, `HydrateProductAction(IReadOnlyList<Product> Items)` | Your feature needs **4 more** actions. See **Every action in this feature** below |
| Reducers (`Store`) | `static class ProductReducers` with `ReduceSelectProductAction`, `ReduceFilterByCategoryAction`, `ReduceHydrateProductAction`, each `(ProductState state, <Action> action)` | Select: `SelectedProduct` = the item in `Items` with that Id, or `null`. Filter: set `FilterCategoryId`. Hydrate: replace `Items` |
| Persistence effect (`Store`) | **Name is yours** (for example `ProductPersistenceEffects`). No test calls it. | When your page opens, load the saved `Items` and dispatch `HydrateProductAction`. After every change, save `Items`. Scored under A9 |
| Product guard (`Guards`) | `static class ProductGuard`, `static bool ShouldRedirect(ProductState state, int productId)` | `true` when no product with that Id is in `Items` |
| Service (`Services`) | `class ProductService : IProductService` | `GetProductById`: success = that product. Failure = no such Id, error code `NotFound`, and the message includes the Id. `GetAllProducts`: success = every product. It has no failure path |
| Pages | `/products`, `/products/{Id:int}` | The detail page calls the guard and redirects to `/products` |

#### Every action in this feature {.part}

The contract lists **3** of the **7** actions this feature needs. The other 4 are **Yours**.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `LoadProductsAction` | Yours | List page, when it opens | Sets `IsLoading` to `true`. Your **service effect** runs on this action |
| `LoadProductsSuccessAction(IReadOnlyList<Product> Products)` | Yours | Your service effect, when `GetAllProducts` succeeds | Replaces `Items`, sets `IsLoading` to `false` |
| `LoadProductsFailureAction(string Message)` | Yours | Your service effect, when the service fails | Sets `IsLoading` to `false` and stores the message |
| `FilterByCategoryAction(int? CategoryId)` | **Fixed** | List page, when the user picks a category (`null` = all) | Sets `FilterCategoryId`. The page shows only the items in that category |
| `SelectProductAction(int ProductId)` | **Fixed** | Detail page, after the guard passes | Sets `SelectedProduct` |
| `HydrateProductRequestAction` | Yours | List page, after its first render | None. Your persistence effect reads local storage and dispatches the next action |
| `HydrateProductAction(IReadOnlyList<Product> Items)` | **Fixed** | Your persistence effect | Replaces `Items` |

Your detail page also dispatches `AddToCartAction` and `AddToWishlistAction`. They are already in
the starter, and the Shopping Cart and Wishlist students write their reducers.

**Easy to mix up:**

- `SelectProductAction` and `FilterByCategoryAction`: Select picks **one product** for the detail
  page. Filter records **which category** the list page shows. Filter does not remove anything
  from `Items`. It only sets `FilterCategoryId`, and the list page uses it to choose what to show.
- `LoadProductsSuccessAction` and `HydrateProductAction`: both replace `Items`. Load success
  brings the list from your service. Hydrate brings back the list saved in local storage.

#### Build it in this order {.part}

1. Create the `Store` and `Guards` folders in `Features/ProductCatalog`.
2. Write `ProductState`, and give it an initial state that loads `Items` from
   `InventorySeedData.Products`.
3. Write all 7 action records from the table above.
4. Write the three fixed reducers. Run the `InventoryBrowser.Tests.ProductCatalog` tests. Then
   write the reducers for your own actions.
5. Write `ProductGuard`, and run the tests again.
6. Write `ProductService`, then uncomment your line in `Program.cs`.
7. Write your service effect for `LoadProductsAction`: wait with `await Task.Delay(300);`, call
   `GetAllProducts`, and dispatch the success or failure action.
8. Build the list page. Dispatch `LoadProductsAction` when it opens, add a category filter, and
   link each product to its detail page.
9. Build the detail page. Call the guard first and redirect if needed. Then dispatch
   `SelectProductAction`, and add the **Add to cart** and **Add to wishlist** buttons.
10. Write your persistence effect (**State Persistence**).
11. Write your own tests in `InventoryBrowser.StudentTests`.

**Done when** every item under [Acceptance Criteria → Product Catalog](#criteria-product-catalog) is true.

### Shopping Cart {#contract-shopping-cart .feature}

#### The business need {.part}

Shoppers rarely buy one thing at a time. They collect items while they browse, and they decide what
to buy at the end. The store needs a cart that keeps a running total, so a shopper always knows
what they are about to spend. It also needs to stop anyone reaching checkout with nothing to buy.

#### What the shopper does, in order {.part}

1. On `/cart`, see a list of the store's products, each with its price and an **Add to cart** button.
2. Click **Add to cart** on the products they want. Each one appears in their cart on the same page.
3. Review the cart: change quantities, remove items, and watch the total update.
4. Click **Proceed to checkout** to go to `/cart/checkout` and see the order summary.

#### Cart page — `/cart` {.part}

As a shopper, I want to pick products and see everything I plan to buy and what it costs, so
that I can change my mind before I check out.

- I see a list of the products I can buy, from `InventorySeedData.Products`, each with an
  **Add to cart** button. This list is on the cart page, so the cart works even if the Product
  Catalog pages do not exist yet.
- Adding a product that is already in my cart increases its quantity. It does not add a second row.
- I see each item in my cart, with its quantity, and the total price.
- I can change an item's quantity. Setting it to 0 removes the item.
- I can remove an item.
- When my cart is empty, the page says so, and the product list is still there to add from.
- A **Proceed to checkout** button or link takes me to `/cart/checkout`.
- My cart is still there after I refresh the browser.

#### Checkout page — `/cart/checkout` {.part}

As a shopper, I want to review my order before I pay, so that I know exactly what I am buying.

- I see a summary of my items, the item count, and the total.
- If my cart is empty, I am sent back to `/cart`, because there is nothing to check out.

#### Shopping Cart contract (build to spec) {.part}

| Piece | Exact contract | Behaviour |
|---|---|---|
| State (`Store`) | `record CartState` with `IReadOnlyList<CartItem> Items`, `decimal Total`, `int ItemCount` | Starts empty |
| Actions (`Store`) | `AddToCartAction(int ProductId, string Name, decimal Price)` **(already in the starter)**, `RemoveFromCartAction(int ProductId)`, `UpdateQuantityAction(int ProductId, int NewQuantity)`, `HydrateCartAction(IReadOnlyList<CartItem> Items)` | Your feature needs **4 more** actions. See **Every action in this feature** below |
| Reducers (`Store`) | `static class CartReducers` with `ReduceAddToCartAction`, `ReduceRemoveFromCartAction`, `ReduceUpdateQuantityAction`, `ReduceHydrateCartAction`, each `(CartState state, <Action> action)` | Add: a new product is added with quantity 1. A product already in the cart has its quantity increased by 1. Remove: remove that product. Update: set the quantity, and a quantity of 0 or less removes the item. Hydrate: replace `Items`. **After every change** recalculate `Total` (price × quantity, summed) and `ItemCount` (quantities, summed) |
| Persistence effect (`Store`) | **Name is yours** (for example `CartPersistenceEffects`). No test calls it. | When your page opens, load the saved `Items` and dispatch `HydrateCartAction`. After every change, save `Items`. Scored under A9 |
| Cart guard (`Guards`) | `static class CartGuard`, `static bool ShouldRedirectFromCheckout(CartState state)` | `true` when the cart is empty. Checkout has no Id, so this guard has no Id parameter |
| Service (`Services`) | `class CartService : ICartService` | `CalculateCartTotal`: success = price × quantity summed, and an empty cart returns a success with a value of 0. Failure = any item with a quantity below 0, error code `CartService.NegativeQuantity`, and the message identifies the item |
| Pages | `/cart`, `/cart/checkout` | The cart page lets the user **add a product from `InventorySeedData.Products`** without using the Product Catalog. Checkout calls the guard and redirects to `/cart` |

#### Every action in this feature {.part}

The contract lists **4** of the **8** actions this feature needs. The other 4 are **Yours**.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `AddToCartAction(int ProductId, string Name, decimal Price)` | **Fixed** (in the starter) | Cart page **Add to cart**. Also Product Catalog's detail page and Wishlist's move-to-cart effect | Adds the product or increases its quantity, then recalculates `Total` and `ItemCount` |
| `UpdateQuantityAction(int ProductId, int NewQuantity)` | **Fixed** | Cart page, when the user changes a quantity | Sets the quantity. 0 or less removes the item. Recalculates |
| `RemoveFromCartAction(int ProductId)` | **Fixed** | Cart page **Remove** | Removes the item. Recalculates |
| `CalculateTotalAction` | Yours | Your effect, after a cart change | None. Your **service effect** runs on this action |
| `CalculateTotalSuccessAction(decimal Total)` | Yours | Your service effect, when `CalculateCartTotal` succeeds | Sets `Total` |
| `CalculateTotalFailureAction(string Message)` | Yours | Your service effect, when the service fails | Stores the message |
| `HydrateCartRequestAction` | Yours | Cart page, after its first render | None. Your persistence effect reads local storage and dispatches the next action |
| `HydrateCartAction(IReadOnlyList<CartItem> Items)` | **Fixed** | Your persistence effect | Replaces `Items`. Recalculates |

The checkout page dispatches no actions. It reads `CartState` and calls the guard.

**Easy to mix up:**

- `AddToCartAction` and `UpdateQuantityAction`: Add **increases** the quantity by 1. Update
  **sets** the quantity to the number given.
- `RemoveFromCartAction` and `UpdateQuantityAction` with 0: both take the item out of the cart.
  Remove is for the **Remove** button. Update with 0 is what happens when the user types 0.
- The reducers and `CalculateTotalAction` both produce `Total`. Your reducers recalculate it after
  every change, because the contract requires it. Your service effect also asks `CartService` for
  it, because the effect is how your feature uses its service. The two values should always match.

#### Build it in this order {.part}

1. Create the `Guards` folder in `Features/ShoppingCart`. The `Store` folder and
   `AddToCartAction` already exist.
2. Write `CartState`. It starts empty.
3. Write the other 7 action records from the table above.
4. Write the four fixed reducers. Write the total and count calculation once, in a helper, and
   call it from each reducer. Run the `InventoryBrowser.Tests.ShoppingCart` tests. Then write the
   reducers for your own actions.
5. Write `CartGuard`, and run the tests again.
6. Write `CartService`, then uncomment your line in `Program.cs`.
7. Write your service effect. After a cart change it dispatches `CalculateTotalAction`. On that
   action, wait with `await Task.Delay(300);`, call `CalculateCartTotal`, and dispatch the success
   or failure action.
8. Build the cart page: the product list with **Add to cart** buttons, the cart items with
   quantity and **Remove** controls, the total, and the link to checkout.
9. Build the checkout page. Call the guard first and redirect if the cart is empty.
10. Write your persistence effect (**State Persistence**).
11. Write your own tests in `InventoryBrowser.StudentTests`.

**Done when** every item under [Acceptance Criteria → Shopping Cart](#criteria-shopping-cart) is true.

### Wishlist {#contract-wishlist .feature}

#### The business need {.part}

Shoppers often find something they like but are not ready to buy it yet. The store wants them to
save it instead of leaving, so they come back to it later. When a shopper is ready, one click
should move the item into the cart.

#### List page — `/wishlist` {.part}

As a shopper, I want to save products I might buy later, so that I do not forget them.

- I see the products I saved.
- I can add a product from the store's catalogue on this page, even if the Product Catalog pages do
  not exist yet.
- Saving a product that is already on my wishlist does not add it twice.
- I can remove a product from my wishlist.
- I can move a product to my cart. It leaves my wishlist and appears in my cart.
- My wishlist is still there after I refresh the browser.

#### Detail page — `/wishlist/{id}` {.part}

As a shopper, I want to see the details of a product I saved, so that I can remember why I saved it.

- I see the product's name, description, and price.
- If I open an item that is not on my wishlist, I am sent back to `/wishlist`. With an empty
  wishlist, every item sends me back.

#### Wishlist contract (build to spec) {.part}

| Piece | Exact contract | Behaviour |
|---|---|---|
| State (`Store`) | `record WishlistState` with `IReadOnlyList<Product> Items`, `Product? SelectedItem` | Starts empty |
| Actions (`Store`) | `AddToWishlistAction(Product Product)` **(already in the starter)**, `RemoveFromWishlistAction(int ProductId)`, `MoveToCartAction(int ProductId)`, `HydrateWishlistAction(IReadOnlyList<Product> Items)` | Your feature needs **5 more** actions, including the one that sets `SelectedItem`. See **Every action in this feature** below |
| Reducers (`Store`) | `static class WishlistReducers` with `ReduceAddToWishlistAction`, `ReduceRemoveFromWishlistAction`, `ReduceMoveToCartAction`, `ReduceHydrateWishlistAction`, each `(WishlistState state, <Action> action)` | Add: no duplicates. A product already in the wishlist is not added twice. Remove: remove that product. Move to cart: remove that product from the wishlist. An **effect** dispatches `AddToCartAction` to put it in the cart. Hydrate: replace `Items` |
| Persistence effect (`Store`) | **Name is yours** (for example `WishlistPersistenceEffects`). No test calls it. | When your page opens, load the saved `Items` and dispatch `HydrateWishlistAction`. After every change, save `Items`. Scored under A9 |
| Wishlist guard (`Guards`) | `static class WishlistGuard`, `static bool ShouldRedirect(WishlistState state, int id)` | `true` when no item with that Id is in `Items`. This also applies to an empty wishlist |
| Service (`Services`) | `class WishlistService : IWishlistService` | `GetWishlistItem(IReadOnlyList<Product> wishlistItems, int productId)`: success = the item with that Id. Failure = not in the list (including an empty list), error code `NotFound`, and the message includes the Id |
| Pages | `/wishlist`, `/wishlist/{Id:int}` | The list page lets the user **add a product from `InventorySeedData.Products`** without using the Product Catalog. The detail page calls the guard and redirects to `/wishlist` |

#### Every action in this feature {.part}

The contract lists **4** of the **9** actions this feature needs. The other 5 are **Yours**.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `AddToWishlistAction(Product Product)` | **Fixed** (in the starter) | Wishlist page **Add**. Also Product Catalog's detail page | Adds the product, unless it is already there |
| `RemoveFromWishlistAction(int ProductId)` | **Fixed** | Wishlist page **Remove** | Removes the product |
| `MoveToCartAction(int ProductId)` | **Fixed** | Wishlist page **Move to cart** | Removes the product. Your move-to-cart effect then dispatches `AddToCartAction` |
| `MoveToCartFailureAction(string Message)` | Yours | Your move-to-cart effect, when the product cannot be found in `InventorySeedData.Products` | Stores the message |
| `SelectWishlistItemAction(int ProductId)` | Yours | Detail page, after the guard passes | None. Your **service effect** runs on this action |
| `SelectWishlistItemSuccessAction(Product Item)` | Yours | Your service effect, when `GetWishlistItem` succeeds | Sets `SelectedItem` |
| `SelectWishlistItemFailureAction(string Message)` | Yours | Your service effect, when the service fails | Stores the message |
| `HydrateWishlistRequestAction` | Yours | Wishlist page, after its first render | None. Your persistence effect reads local storage and dispatches the next action |
| `HydrateWishlistAction(IReadOnlyList<Product> Items)` | **Fixed** | Your persistence effect | Replaces `Items` |

**Easy to mix up:**

- `RemoveFromWishlistAction` and `MoveToCartAction`: both take the product off the wishlist.
  Remove stops there. Move to cart also puts the product in the cart, through your effect.
- `AddToWishlistAction` and `AddToCartAction`: Add to wishlist saves the product for later. Add to
  cart is the Shopping Cart's action, which your move-to-cart effect dispatches.

#### Build it in this order {.part}

1. Create the `Guards` folder in `Features/Wishlist`. The `Store` folder and
   `AddToWishlistAction` already exist.
2. Write `WishlistState`. It starts empty.
3. Write the other 8 action records from the table above.
4. Write the four fixed reducers. Run the `InventoryBrowser.Tests.Wishlist` tests. Then write the
   reducers for your own actions.
5. Write `WishlistGuard`, and run the tests again.
6. Write `WishlistService`, then uncomment your line in `Program.cs`.
7. Write your two effects. The move-to-cart effect finds the product in
   `InventorySeedData.Products` and dispatches `AddToCartAction`, or the failure action. The
   service effect waits with `await Task.Delay(300);`, calls `GetWishlistItem`, and dispatches the
   success or failure action.
8. Build the list page: the catalogue with **Add** buttons, and your saved items with **Remove**
   and **Move to cart** buttons, each linking to its detail page.
9. Build the detail page. Call the guard first and redirect if needed. Then dispatch
   `SelectWishlistItemAction` and show `SelectedItem`.
10. Write your persistence effect (**State Persistence**).
11. Write your own tests in `InventoryBrowser.StudentTests`.

**Done when** every item under [Acceptance Criteria → Wishlist](#criteria-wishlist) is true.

### Stock Alerts {#contract-stock-alerts .feature}

#### The business need {.part}

The store loses sales when a popular product is out of stock. Store staff need to watch the
products that matter and see a warning when stock falls to a level they choose, so they can
reorder in time.

#### List page — `/alerts` {.part}

As a store staff member, I want to set a low-stock alert on a product, so that I know when to
reorder it.

- I see every alert I have set, and how many are triggered right now.
- For each alert, I see whether stock is low or OK.
- I can create an alert for a product. A product has only one alert.
- I can delete an alert.
- My alerts are still there after I refresh the browser.

#### Detail page — `/alerts/{productId}` {.part}

As a store staff member, I want to see one alert in full, so that I can decide whether to reorder
now.

- I see the product, the alert level, and whether the alert is triggered.
- If I open an alert for a product that has no alert, I am sent back to `/alerts`.

#### Stock Alerts contract (build to spec) {.part}

| Piece | Exact contract | Behaviour |
|---|---|---|
| Model (`Models`) | `record StockAlert(int ProductId, string ProductName, int ThresholdQuantity, bool IsTriggered)` | |
| State (`Store`) | `record AlertState` with `IReadOnlyList<StockAlert> Alerts`, `int ActiveCount` | Starts empty |
| Actions (`Store`) | `CreateAlertAction(int ProductId, string ProductName, int ThresholdQuantity)`, `DeleteAlertAction(int ProductId)`, `CheckStockSuccessAction(int ProductId, bool IsTriggered)`, `HydrateAlertAction(IReadOnlyList<StockAlert> Alerts)` | Your feature needs **3 more** actions, including the one that starts the stock check. See **Every action in this feature** below |
| Reducers (`Store`) | `static class AlertReducers` with `ReduceCreateAlertAction`, `ReduceDeleteAlertAction`, `ReduceCheckStockSuccessAction`, `ReduceHydrateAlertAction`, each `(AlertState state, <Action> action)` | Create: add an alert with `IsTriggered = false`, but only when that product does not already have an alert. Delete: remove the alert for that product. Check stock: set `IsTriggered` on that product's alert. Hydrate: replace `Alerts`. **After every change** set `ActiveCount` to the number of triggered alerts |
| Persistence effect (`Store`) | **Name is yours** (for example `AlertPersistenceEffects`). No test calls it. | When your page opens, load the saved `Alerts` and dispatch `HydrateAlertAction`. After every change, save `Alerts`. Scored under A9 |
| Alert guard (`Guards`) | `static class AlertGuard`, `static bool ShouldRedirect(AlertState state, int productId)` | `true` when no alert exists for that product |
| Service (`Services`) | `class AlertService : IAlertService` | `CheckStock(int productId)`: success = that product's stock quantity, and a quantity of 0 is still a success. Failure = no such product, error code `NotFound`, and the message includes the Id |
| Pages | `/alerts`, `/alerts/{ProductId:int}` | The detail page calls the guard and redirects to `/alerts` |

#### Every action in this feature {.part}

The contract lists **4** of the **7** actions this feature needs. The other 3 are **Yours**.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `CreateAlertAction(int ProductId, string ProductName, int ThresholdQuantity)` | **Fixed** | Alerts page, when the user creates an alert | Adds the alert with `IsTriggered = false`, unless that product already has one. Your effect then dispatches `CheckStockAction` |
| `DeleteAlertAction(int ProductId)` | **Fixed** | Alerts page **Delete** | Removes the alert. Recalculates `ActiveCount` |
| `CheckStockAction(int ProductId, int ThresholdQuantity)` | Yours | Your effect, after an alert is created | None. Your **service effect** runs on this action |
| `CheckStockSuccessAction(int ProductId, bool IsTriggered)` | **Fixed** | Your service effect, when `CheckStock` succeeds. `IsTriggered` is `true` when stock is at or below the threshold | Sets `IsTriggered` on that alert. Recalculates `ActiveCount` |
| `CheckStockFailureAction(int ProductId, string Message)` | Yours | Your service effect, when the service fails | Stores the message |
| `HydrateAlertRequestAction` | Yours | Alerts page, after its first render | None. Your persistence effect reads local storage and dispatches the next action |
| `HydrateAlertAction(IReadOnlyList<StockAlert> Alerts)` | **Fixed** | Your persistence effect | Replaces `Alerts`. Recalculates `ActiveCount` |

The detail page dispatches no actions. It reads `AlertState` and calls the guard.

**Easy to mix up:**

- `CreateAlertAction` and `CheckStockAction`: Create **adds** the alert, always with
  `IsTriggered = false`. Check stock **finds out** whether stock is low, and its success action
  sets the real `IsTriggered` value.
- `CheckStockAction` and `CheckStockSuccessAction`: the first one **asks** for a stock check, and
  your effect runs on it. The second one **carries the answer** back to your reducer.

#### Build it in this order {.part}

1. Create the `Models`, `Store` and `Guards` folders in `Features/StockAlerts`.
2. Write the `StockAlert` record, then `AlertState`. It starts empty.
3. Write all 7 action records from the table above.
4. Write the four fixed reducers. Write the `ActiveCount` calculation once, in a helper, and call it
   from each reducer. Run the `InventoryBrowser.Tests.StockAlerts` tests. Then write the reducers
   for your own actions.
5. Write `AlertGuard`, and run the tests again.
6. Write `AlertService`, then uncomment your line in `Program.cs`.
7. Write your service effect. After `CreateAlertAction` it dispatches `CheckStockAction`. On that
   action, wait with `await Task.Delay(300);`, call `CheckStock`, compare the stock with the
   threshold, and dispatch the success or failure action.
8. Build the alerts page: a form to create an alert, the list of alerts with low or OK status and
   a **Delete** button, the triggered count, and a link to each alert's detail page.
9. Build the detail page. Call the guard first and redirect if needed.
10. Write your persistence effect (**State Persistence**).
11. Write your own tests in `InventoryBrowser.StudentTests`.

**Done when** every item under [Acceptance Criteria → Stock Alerts](#criteria-stock-alerts) is true.

### Category Manager {#contract-category-manager .feature}

#### The business need {.part}

The store groups its products into categories. Store staff need to see every category and check
which products belong to each one, so the catalogue stays organised.

#### List page — `/categories` {.part}

As a store staff member, I want to see every category, so that I know how the catalogue is
organised.

- I see a list of all categories.
- I can open any category to see its details.

#### Detail page — `/categories/{id}` {.part}

As a store staff member, I want to see one category and the products in it, so that I can check
that each product is in the right category.

- I see the category's name and description.
- I see the products that belong to this category.
- If I open a category that does not exist, I am sent back to `/categories`.

#### Category Manager contract (build to spec) {.part}

| Piece | Exact contract | Behaviour |
|---|---|---|
| State (`Store`) | `record CategoryState` with `IReadOnlyList<Category> Categories`, `Category? SelectedCategory`, `IReadOnlyList<Product> FilteredProducts` | The initial state loads `Categories` from `InventorySeedData.Categories` |
| Actions (`Store`) | `LoadCategoriesSuccessAction(IReadOnlyList<Category> Categories)`, `SelectCategoryAction(int CategoryId)`, `FilterProductsAction(int CategoryId)`, `HydrateCategoryAction(IReadOnlyList<Category> Categories)` | Your feature needs **4 more** actions, including the one that starts the load. See **Every action in this feature** below |
| Reducers (`Store`) | `static class CategoryReducers` with `ReduceLoadCategoriesSuccessAction`, `ReduceSelectCategoryAction`, `ReduceFilterProductsAction`, `ReduceHydrateCategoryAction`, each `(CategoryState state, <Action> action)` | Load and Hydrate: replace `Categories`. Select: `SelectedCategory` = the category with that Id, or `null`. Filter: `FilteredProducts` = the products in `InventorySeedData.Products` with that `CategoryId` |
| Persistence effect (`Store`) | **Name is yours** (for example `CategoryPersistenceEffects`). No test calls it. | When your page opens, load the saved `Categories` and dispatch `HydrateCategoryAction`. After every change, save `Categories`. Scored under A9 |
| Category guard (`Guards`) | `static class CategoryGuard`, `static bool ShouldRedirect(CategoryState state, int categoryId)` | `true` when no category with that Id is in `Categories` |
| Service (`Services`) | `class CategoryService : ICategoryService` | `GetCategoryById(int id)`: success = that category. Failure = no such Id, error code `NotFound`, and the message includes the Id |
| Pages | `/categories`, `/categories/{Id:int}` | The detail page calls the guard and redirects to `/categories` |

#### Every action in this feature {.part}

The contract lists **4** of the **8** actions this feature needs. The other 4 are **Yours**.

| Action | Name | Dispatched by | What its reducer does |
|---|---|---|---|
| `LoadCategoriesAction` | Yours | List page, when it opens | None. Your load effect runs on this action |
| `LoadCategoriesSuccessAction(IReadOnlyList<Category> Categories)` | **Fixed** | Your load effect, with `InventorySeedData.Categories` | Replaces `Categories` |
| `LoadCategoriesFailureAction(string Message)` | Yours | Your load effect, when there are no categories | Stores the message |
| `SelectCategoryAction(int CategoryId)` | **Fixed** | Detail page, after the guard passes | Sets `SelectedCategory`. Your **service effect** also runs on this action |
| `FilterProductsAction(int CategoryId)` | **Fixed** | Your service effect, when `GetCategoryById` succeeds. Also a **Refresh products** button, if you add one | Sets `FilteredProducts` |
| `SelectCategoryFailureAction(string Message)` | Yours | Your service effect, when the service fails | Stores the message |
| `HydrateCategoryRequestAction` | Yours | List page, after its first render | None. Your persistence effect reads local storage and dispatches the next action |
| `HydrateCategoryAction(IReadOnlyList<Category> Categories)` | **Fixed** | Your persistence effect | Replaces `Categories` |

`ICategoryService` has no method that returns every category, so your load effect reads
`InventorySeedData.Categories` directly. The effect that calls your service is the one that runs on
`SelectCategoryAction`.

**Easy to mix up:**

- `SelectCategoryAction` and `FilterProductsAction`: Select finds the **category** (its name and
  description). Filter finds the **products** in it. The detail page needs both, with the same Id.
- `LoadCategoriesSuccessAction` and `HydrateCategoryAction`: both replace `Categories`. Load
  success brings the list from `InventorySeedData`. Hydrate brings back the list saved in local
  storage.

#### Why Category Manager works the way it does {.part}

Category Manager is the one feature whose data the user cannot change. The categories come from
`InventorySeedData`, and no page adds, edits, or removes one. A few of its requirements may look
odd because of that. They are deliberate, and here is why:

- **You save and restore `Categories`, even though the list never changes.** Every feature in
  this lab practises the same save-and-restore pattern from Day 8, and it is graded the same way
  for everyone. In your feature a refresh looks the same with or without it. Build it anyway.
  A refresh cannot show you that it worked, so check it in the browser instead.
  To check that your save works:

  1. Run the app and open `/categories`.
  2. Press **F12** to open the developer tools.
  3. Click the **Application** tab. If you do not see it, click **»** to find it.
  4. In the left panel, expand **Local storage** and click your app's address (for example
     `https://localhost:7123`).
  5. Find your key in the list, for example `inventory.category-manager`. Its value is your saved
     categories, as JSON text. If the key is there, your save works.
- **Your load effect's failure branch never runs.** The seed data always has five categories, so
  `LoadCategoriesFailureAction` never fires in this lab. Write it and its reducer anyway: every
  effect handles both outcomes, because a real data source can fail.
- **Your service effect runs on `SelectCategoryAction`, not on the load.** `ICategoryService` has
  only `GetCategoryById`, so the real success-or-failure flow is the detail page checking one
  category. That is where an unknown Id gives you a real failure.
- **Select and Filter are two actions, even though the detail page uses both.** Each one changes
  one part of the state, so each reducer can be tested on its own. A **Refresh products** button
  can also dispatch Filter by itself.

#### Build it in this order {.part}

1. Create the `Store` and `Guards` folders in `Features/CategoryManager`.
2. Write `CategoryState`, and give it an initial state that loads `Categories` from
   `InventorySeedData.Categories`.
3. Write all 8 action records from the table above.
4. Write the four fixed reducers. Run the `InventoryBrowser.Tests.CategoryManager` tests. Then
   write the reducers for your own actions.
5. Write `CategoryGuard`, and run the tests again.
6. Write `CategoryService`, then uncomment your line in `Program.cs`.
7. Write your two effects. The load effect waits with `await Task.Delay(300);` and dispatches the
   success or failure action. The service effect runs on `SelectCategoryAction`, calls
   `GetCategoryById`, and dispatches `FilterProductsAction` or the failure action.
8. Build the list page. Dispatch `LoadCategoriesAction` when it opens, and link each category to
   its detail page.
9. Build the detail page. Call the guard first and redirect if needed. Then dispatch
   `SelectCategoryAction`, and show `SelectedCategory` and `FilteredProducts`.
10. Write your persistence effect (**State Persistence**).
11. Write your own tests in `InventoryBrowser.StudentTests`.

**Done when** every item under [Acceptance Criteria → Category Manager](#criteria-category-manager) is true.

---

## Auto-Testing

Your instructor has a test suite for each feature. The starter code includes the **visible** half of it, and GitHub Actions runs those visible tests on every push to the repository. The **hidden** half runs only when your instructor grades the lab.

### How It Works
- **Visible tests (~50%):** Included in the starter code in `tests/InventoryBrowser.Tests/`, one folder per feature. You can see the test code, run tests locally, and use them to guide your development. In Test Explorer, run the tests under your feature's namespace (for example `InventoryBrowser.Tests.Wishlist`).
- **Hidden tests (~50%):** Not included in the starter code, and GitHub Actions does not run them. Your instructor runs them when grading, and your result appears in your mark. These hidden tests check edge cases. They also catch code that is written only to pass the visible tests. The hidden tests check the edge cases in the **Behaviour** column of your feature's contract table (see Feature Contracts above), for example adding a product that is already there, or asking for an Id that does not exist. Any rule in that column can be tested, so build every rule in it, not only the ones the visible tests check.

### What the Tests Cover (per feature)
| Category | Visible | Hidden | Internal weight |
|----------|---------|--------|-----------------|
| Reducer tests (state transitions) | 2 | 2 or 3 | ~33% of pass rate |
| Guard logic tests (redirect conditions) | 1 | 1 | ~33% of pass rate |
| BYSResult service tests (success/failure) | 1 | 1 or 2 | ~33% of pass rate |

**How many tests your feature has:**

| Your feature | Tests |
|---|---|
| Product Catalog | 8 |
| Shopping Cart | 10 |
| Wishlist, Stock Alerts, Category Manager | 9 |

> **The counts differ by feature, and that is deliberate.** Product Catalog has one fewer reducer
> test because its state-persistence test replaced an existing one that checked the same thing.
> Shopping Cart has one extra service test, for the empty-cart case.
>
> **The different counts do not help or hurt anyone.** Your auto-test score is a **pass rate**, not a
> count of failures. A number of failed tests (for example, "I failed two tests") is not a grade until
> you divide it by your feature's test count. GitHub Actions shows your pass rate on the visible tests
> only. Your graded pass rate includes the hidden tests.

Auto-test results contribute to the **Auto-Tests (16 pts)** line inside the Functionality bucket of the rubric. Scoring is **band-based**, not per-test arithmetic: your instructor runs the visible and hidden tests together and applies the band table from rubric row F8 to that combined pass rate (90–100% = 16 pts, 75–89% = 12, 50–74% = 8, 25–49% = 4, < 25% = 0). The "Internal weight" column above is for prioritization while you debug — it does not directly multiply into a points total. The visible tests are your development guide; if they pass, your work is going in the right direction.

---

## Starter Code

The starter code is a Blazor Web App that includes:
- Fluxor NuGet packages pre-configured, with Redux DevTools integration
- Basic app shell with navigation layout
- Shared models (`Product`, `Category`, `CartItem`) and the `InventorySeedData` catalogue
- Browser local storage registered for state persistence
- The shared contract: one service interface per feature, plus `AddToCartAction` and `AddToWishlistAction` (see Feature Contracts)
- Visible instructor tests in `tests/InventoryBrowser.Tests/`, one folder per feature
- An empty `tests/InventoryBrowser.StudentTests/` project for your own tests
- "Your First PR" guide for team Git workflow

**You write everything else in your feature yourself**, using your contract table: the state,
actions, reducers, effects, guard, service class, and pages. The Day 4-8 reference sheets show
each pattern.

### Shared files -- leave them as they are

These starter files belong to the whole team. Do not edit them:

- **`Components/Pages/Home.razor`** -- the landing page. Keep it. It is not graded.
- **`Components/Layout/NavMenu.razor`** -- it already has a link to every feature's list page.
- **`Components/Layout/MainLayout.razor`** -- restore your state from your own page, not from
  here (see **State Persistence**).

The one shared file you do change is `Program.cs`: uncomment **your own** service line, and only
that line. If five students all edit the same shared file, you get merge conflicts on every pull
request.

---

## Your Feature README

Your architecture notes go in **your own feature folder**, in `Features/<YourFeature>/README.md`
(for example `Features/ShoppingCart/README.md`). From the root of your repository, the full path is
`src/InventoryBrowser/Features/<YourFeature>/README.md`. The starter includes this file with the four
headings below. Nobody else edits it, so it never causes a merge conflict, and it shows your work
under your own name. It is scored as **IC6** in the rubric.

Answer each heading in 2 to 4 sentences, about your own code:

1. **State.** List the fields in your state record. For any field you added beyond your contract
   table, say why you need it.
2. **Actions and effects.** Name the actions you added beyond the contract table, and your
   effect. Say what your effect does, and which success action and failure action it dispatches.
3. **Guard.** Explain what your guard checks, and where your page calls it.
4. **One decision.** Describe one choice you made and one alternative you rejected, with the
   reason. Examples: where you put a calculation, how you named your storage key, or how your
   effect handles a failure.

The team's root `README.md` does not need an Architecture section. It links to each feature's
README.

---

## Seed Data and Resilience

The starter ships **`InventorySeedData`, the shared catalogue**. Every team has the same products and categories to build against. The Product Catalog and Category Manager states start from it (their contract tables say so). The Shopping Cart, Wishlist, and Stock Alerts states start **empty**. Those features hold no data until a user does something, and writing the code that fills them is your work. Every feature can reach the catalogue directly, so **no feature needs another student's feature in order to work**: Shopping Cart and Wishlist each let the user add products from the catalogue on their own page.

This serves two purposes:

1. **Development convenience** -- You can build your UI against real products and categories immediately, instead of waiting on a teammate's service to return data
2. **Team resilience** -- Each feature slice reads its own portion of the store independently, so an incomplete feature elsewhere cannot break yours

**An unbuilt feature is simply absent.** Its pages do not exist yet, so its link in the navigation menu shows a "not found" page. Nothing crashes and no student is blocked by a teammate's incomplete work -- but the app is not feature-complete until every feature is built.

---

## Team Size Scaling

| Team Size | How It Works |
|-----------|-------------|
| **3 students** | Each student takes 1 feature. **Any three features work**, because no feature depends on another to run |
| **4 students** | Each student takes 1 feature; the 5th feature stays unbuilt |
| **5 students** | 1 feature per student (ideal size) |

**Teams with fewer than five students do not need to set anything up.** A feature nobody owns is never started, so
its instructor tests simply fail in Test Explorer and GitHub Actions shows no check for it (a
feature's check appears once its `Store/<X>State.cs` exists). Grading scores each student on their
own feature only, so an unassigned feature does not reduce anyone's marks.

---

## Team Collaboration (CO6 -- Forming)

This is the first lab, so CO6 expectations focus on foundational Git practices:

- Your feature is yours for the whole lab. Your **branches are not**: make one short branch per
  task (`feature/cart-state`, `test/cart-reducers`), merge it through a pull request, delete it,
  and branch again from an updated `main`
- All code enters `main` through **pull requests**
- Each PR must have a description that explains the change
- At least one teammate reviews each PR
- **Review your teammates' PRs.** For full marks, leave at least **two** reviews, each with at
  least one comment: a question, a suggestion, or a requested change. An approval with no comment
  counts as a surface review
- **Commit often.** For full marks, make **10 or more** small commits across the lab period, each
  one logical change, with a conventional message (`feat:`, `fix:`, `test:`)
- Fill in your own row of the **Team Members** table in the root `README.md`

The starter code includes a **"Your First PR"** guide (`YOUR-FIRST-PR.md`) that walks through the
branch, pull request, and review workflow step by step. The
[Git Branching Master Reference](../student/Git-Branching-Master-Reference.html?isCourseFile=true)
covers what to look for in a code review.

### Team Accountability

Team labs in SDEV 2351 build the collaboration skills you will need in SDEV 3001 (Software Development Capstone). Every team member is expected to contribute meaningfully.

**What counts as contribution:**
- Regular Git commits on your own branches
- Pull requests with meaningful descriptions
- Code review comments on teammates' PRs
- Attendance and active participation on in-class lab days (Days 11, 15, 18)

**What happens if a team member is not contributing:**

1. **Warning:** At the mid-point check-in (Day 15), the instructor reviews each team's Git history and progress. If a team member has not contributed (few or no commits, no PRs, no attendance), the instructor will discuss expectations directly with that student.
2. **Removal:** If contribution does not improve after the warning, the student may be removed from the team and receive a **zero** for Lab 1.
3. **Immediate removal:** In extreme cases — zero commits, no attendance on lab days, no pull request or code review activity — the instructor may remove a student without a prior warning.

A removed student cannot complete the lab individually and will receive a grade of zero for that lab. The remaining team members are not penalized for the removal.

---

## Acceptance Criteria by Feature

**Jump to your feature:** [Product Catalog](#criteria-product-catalog) · [Shopping Cart](#criteria-shopping-cart) · [Wishlist](#criteria-wishlist) · [Stock Alerts](#criteria-stock-alerts) · [Category Manager](#criteria-category-manager)

### Product Catalog {#criteria-product-catalog}
- `/products` page displays a list of products (from state or seed data)
- `/products/{id:int}` page displays product details for the selected product
- Navigating to a non-existent product ID redirects to the products list (guard)
- One effect that calls `ProductService` and dispatches a success or failure action (see **State Management**)
- FilterByCategory action filters the displayed product list
- 3+ reducer tests and 1+ guard logic test pass

### Shopping Cart {#criteria-shopping-cart}
- `/cart` page displays cart items with quantities and total
- `/cart` page lets the user add a product from the catalogue (`InventorySeedData.Products`), so the cart works even if no teammate builds Product Catalog
- `/cart/checkout` page shows checkout summary
- Navigating to checkout with an empty cart redirects to `/cart` (guard)
- AddToCart, RemoveFromCart, UpdateQuantity actions update CartState correctly
- One effect that calls `CartService` and dispatches a success or failure action (see **State Management**)
- 3+ reducer tests and 1+ guard logic test pass

### Wishlist {#criteria-wishlist}
- `/wishlist` page displays saved wishlist items
- `/wishlist` page lets the user add a product from the catalogue (`InventorySeedData.Products`), so the wishlist works even if no teammate builds Product Catalog
- `/wishlist/{id:int}` page displays wishlist item details
- Navigating to the detail page for an item that is not in the wishlist redirects to `/wishlist` (guard). An empty wishlist redirects for every Id
- MoveToCart action dispatches both a wishlist removal and a cart addition
- One effect that calls `WishlistService` and dispatches a success or failure action (see **State Management**)
- 3+ reducer tests and 1+ guard logic test pass

### Stock Alerts {#criteria-stock-alerts}
- `/alerts` page displays active stock alerts
- `/alerts/{productId:int}` page displays alert details for a specific product
- Navigating to `/alerts/{productId}` for a product that has no alert redirects to `/alerts` (guard)
- CreateAlert and DeleteAlert actions manage AlertState
- A CheckStock effect that calls `AlertService.CheckStock` and dispatches `CheckStockSuccessAction` or a failure action (see **State Management**)
- 3+ reducer tests and 1+ guard logic test pass

### Category Manager {#criteria-category-manager}
- `/categories` page displays all categories
- `/categories/{id:int}` page displays category details with associated products
- Navigating to a non-existent category redirects to `/categories` (guard)
- One effect that calls `CategoryService` and dispatches a success or failure action (see **State Management**)
- FilterProducts action filters products within a selected category
- 3+ reducer tests and 1+ guard logic test pass

---

## Code Review Expectations

Because every student implements similar patterns (routing + state + guards), code reviews are especially valuable. The code review section of the Git Branching Master Reference (linked above) lists what to check.

When reviewing a teammate's PR:

- Compare their Fluxor slice structure to yours -- are there patterns you can learn from?
- Check that route constraints are appropriate for the data types used
- Verify that state updates are immutable (using records, not mutation)
- Confirm unit tests actually test meaningful state transitions

---

## Definition of Done

Before submitting, every student should confirm their feature meets all of the following:

- Feature builds without warnings
- All visible instructor tests pass locally (green in Test Explorer)
- Your 3+ reducer tests and 1+ guard logic test are written and passing
- BYSResult pattern is implemented in your service layer
- **Your feature's state survives a hard refresh** (Ctrl+Shift+R) -- saved to browser storage
  under a key namespaced to your feature, and restored from your own page
- All your work is merged to `main` through reviewed pull requests
- Your row of the **Team Members** table in the root `README.md` is filled in
- Your feature README (`Features/<YourFeature>/README.md`) answers its four headings
- Your walkthrough recording is uploaded to the **Group Lab 1** folder in Brightspace

---

## Common Pitfalls

**Do not mutate state in reducers.** Reducers must return a new state object using `with` expressions. If you modify a property directly on the existing state, Fluxor will not detect the change and your UI will not update.

**Do not put guard logic in the .razor component.** Extract your guard's decision logic into the static method named in **your feature's** contract table, with exactly that class, method name, and parameters. Four features use `ShouldRedirect(state, id)`. Shopping Cart is different: it uses `CartGuard.ShouldRedirectFromCheckout(state)`, with no Id. The instructor tests call this method directly -- if your logic lives inside a component, the tests cannot reach it.

**Remember to register your feature's services in Program.cs.** If your service is not registered with DI, Blazor will throw a runtime error when your page tries to inject it. Check that `builder.Services.AddScoped<IYourService, YourService>()` is in Program.cs.

---

## What You Hand In

Everything is due by **the deadline on the Course Schedule**. There are three things. The last two are uploaded to the
**Group Lab 1** folder in Brightspace:

| What | Where it goes | Who |
|---|---|---|
| **Your code** -- your feature, your tests, and your feature README | Your team's GitHub Classroom repository. Nothing to upload: your instructor marks what is merged to `main` | Each student, on their own branches, merged through pull requests |
| **Your walkthrough recording** | Upload it to the **Group Lab 1** assignment folder in Brightspace, the same folder as your peer evaluation | Each student |
| **Your peer evaluation** | Upload the filled-in `.docx` to the **Group Lab 1** assignment folder in Brightspace | Each student uploads their own |

What you upload depends on the format you choose. The
[Walkthroughs Guide](Walkthroughs-README.html?isCourseFile=true)
explains the formats, what to upload, and how to submit it. The
[Lab 1 Recording Prompts](Lab-1-Recording-Prompts.html?isCourseFile=true)
list what to cover.

### Citing code you did not write

Outside resources — documentation, tutorials, forums, other people's repositories — are a normal part
of building software, and you are expected to use them. You must still be able to explain everything
you submit.

If a block of code in your submission differs substantially from the patterns demonstrated in
class, add a citation directly at that block:

- A short comment directly above the block naming where it came from, plus one line on what it
  does: `// adapted from https://example.com/post — debounces the search input`
- If you reworked it, say so: `// adapted from <source>; rewritten to go through the service layer`

You do not need to cite the course material, the starter code, or a pattern taught in class. This
applies to code you brought in from outside the course.

Whether AI assistants may be used on this lab is set by the course AI policy, not by this section.
Where their use is permitted, the same attribution rule applies.

**Why this matters.** Your walkthrough is where you explain your code in your own words.
Unattributed code you cannot explain is handled under the Academic Integrity section of the course
syllabus. When you cite the code, it is clear that you used a resource. When you do not cite it, there may be a
question about whether you are the author.

---

## Grading

The [Lab 1 Rubric](Lab-01-Rubric.html?isCourseFile=true) has every criterion and its point levels.

**Summary:**

| Bucket | Weight |
|--------|--------|
| **Architecture** (gateway — pattern fidelity) | 30% |
| **Functionality** (routing, state, guards, auto-test pass rate) | 40% |
| **Individual Contribution** (walkthrough recording 8% + student-authored tests 4% + per-student CO6 git signals 8%) | 20% |
| **Peer Evaluation** (confidential teammate ratings) | 10% |

**Architecture is a gateway.** A sub-check (A1 to A9) fails when it scores in its bottom two levels -- for example 1 or 0 out of 4 on A1. A2 has only three levels (4, 3, 0), so it fails only at 0. 0–1 failed sub-checks = PASS (auto-grade trusted), 2–3 = REVIEW (your instructor looks closely at your walkthrough), 4 or more = FAIL: your Architecture score is **capped at 10 out of 30**, however well your auto-tests pass. Pattern fidelity matters — the auto-test pass rate alone cannot hide a pattern gap.

Architecture and Functionality are assessed **per student's feature module** — your grade is based on **your feature**, not the team's overall application.

**Walkthrough recording (8% inside Individual Contribution):** You record an 8–12 minute walkthrough of your own code, answering 3 mandatory prompts (+ 1 optional). Choose **Format A** (recorded video) or **Format B** (recorded audio + annotated markdown); the format does not affect your grade. **Format C** (live on Teams) is by arrangement only -- contact your instructor at least one week before the due date. The walkthrough is the AI-free ground truth that confirms you personally own the pattern decisions in your feature module. **Read the prompts:** [Lab 1 Recording Prompts](Lab-1-Recording-Prompts.html?isCourseFile=true). Formats, how to submit in Brightspace, and the AI / transcription rules: [Walkthroughs Guide](Walkthroughs-README.html?isCourseFile=true).

**Peer Evaluation (10%, dedicated bucket):** Confidential teammate ratings. Download the fillable form from the Lab 1 page, fill it in, and upload it to the **Group Lab 1** assignment folder by **the Lab 1 deadline on the Course Schedule**. If you do not submit one, your Peer Evaluation score is 0. How the ratings become your score, and what each rating means: [Lab 1 Peer Evaluation Form](Lab-1-Peer-Evaluation-Form.html?isCourseFile=true).

---

**Status:** Final Spec v2.2 (2026-09-28) | **Auto-grading:** GitHub Classroom with instructor test suite (visible/hidden split, band-based scoring)
