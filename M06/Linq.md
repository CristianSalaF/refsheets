# Entity Framework and LINQ Glossary

## Basic LINQ Operations

### Select
Selects specific columns from a table.

```csharp
// Select all columns
var allProducts = context.Products.ToList();

// Select specific columns
var productNames = context.Products
    .Select(p => p.Name)
    .ToList();

// Project into anonymous type
var productInfo = context.Products
    .Select(p => new { p.Name, p.Price })
    .ToList();

// Project into concrete type
var productDTOs = context.Products
    .Select(p => new ProductDTO { Name = p.Name, Price = p.Price })
    .ToList();
```

### Where
Filters records based on a condition.

```csharp
// Basic where
var expensiveProducts = context.Products
    .Where(p => p.Price > 100)
    .ToList();

// Multiple conditions (AND)
var expensiveActiveProducts = context.Products
    .Where(p => p.Price > 100 && p.IsActive)
    .ToList();

// Multiple conditions (OR)
var discountedOrNewProducts = context.Products
    .Where(p => p.IsDiscounted || p.IsNew)
    .ToList();

// String comparison
var searchResults = context.Products
    .Where(p => p.Name.Contains("laptop"))
    .ToList();
```

### OrderBy/OrderByDescending
Sorts the results.

```csharp
// Sort by one property (ascending)
var productsByPrice = context.Products
    .OrderBy(p => p.Price)
    .ToList();

// Sort by one property (descending)
var productsByPriceDesc = context.Products
    .OrderByDescending(p => p.Price)
    .ToList();

// Multiple sorts
var sortedProducts = context.Products
    .OrderBy(p => p.Category)
    .ThenBy(p => p.Price)
    .ToList();

// Combined with Where
var sortedFilteredProducts = context.Products
    .Where(p => p.IsActive)
    .OrderBy(p => p.Price)
    .ToList();
```

### FirstOrDefault / SingleOrDefault / First / Single
Retrieves a single entity.

```csharp
// Get first record
var firstProduct = context.Products.First();

// Get first record or null
var product = context.Products
    .FirstOrDefault(p => p.Id == 1);

// Get single record (throws if multiple found)
var singleProduct = context.Products
    .Single(p => p.Id == 1);

// Get single record or null
var maybeProduct = context.Products
    .SingleOrDefault(p => p.Code == "ABC123");
```

### Take / Skip
Pagination.

```csharp
// Get first 10 records
var firstPage = context.Products
    .Take(10)
    .ToList();

// Skip first 10, take next 10
var secondPage = context.Products
    .Skip(10)
    .Take(10)
    .ToList();

// Ordered pagination
var page = context.Products
    .OrderBy(p => p.Name)
    .Skip((pageNumber - 1) * pageSize)
    .Take(pageSize)
    .ToList();
```

## Intermediate LINQ Operations

### Join
Performs a join between two tables.

```csharp
// Join using navigation properties (preferred in EF)
var productsWithCategory = context.Products
    .Include(p => p.Category)
    .ToList();

// Explicit join
var query = from p in context.Products
            join c in context.Categories on p.CategoryId equals c.Id
            select new { ProductName = p.Name, CategoryName = c.Name };

// Method syntax join
var result = context.Products
    .Join(
        context.Categories,
        product => product.CategoryId,
        category => category.Id,
        (product, category) => new { 
            ProductName = product.Name, 
            CategoryName = category.Name 
        }
    )
    .ToList();
```

### GroupBy
Groups data by a specific column.

```csharp
// Group products by category
var groupedProducts = context.Products
    .GroupBy(p => p.CategoryId)
    .Select(g => new {
        CategoryId = g.Key,
        ProductCount = g.Count(),
        Products = g.ToList()
    })
    .ToList();

// Group with aggregate
var categorySummary = context.Products
    .GroupBy(p => p.CategoryId)
    .Select(g => new {
        CategoryId = g.Key,
        TotalProducts = g.Count(),
        AveragePrice = g.Average(p => p.Price),
        MaxPrice = g.Max(p => p.Price)
    })
    .ToList();

// Group by multiple properties
var groupByMultiple = context.Products
    .GroupBy(p => new { p.CategoryId, p.SupplierId })
    .Select(g => new {
        CategoryId = g.Key.CategoryId,
        SupplierId = g.Key.SupplierId,
        Products = g.ToList()
    })
    .ToList();
```

### Count / Sum / Min / Max / Average
Aggregation functions.

```csharp
// Count
var totalProducts = context.Products.Count();
var activeProducts = context.Products.Count(p => p.IsActive);

// Sum
var totalValue = context.Products.Sum(p => p.Price * p.Stock);

// Min/Max
var cheapestPrice = context.Products.Min(p => p.Price);
var mostExpensivePrice = context.Products.Max(p => p.Price);

// Average
var averagePrice = context.Products.Average(p => p.Price);

// Combined with Where
var avgPriceOfActiveProducts = context.Products
    .Where(p => p.IsActive)
    .Average(p => p.Price);
```

### Any / All
Checks if any or all elements satisfy a condition.

```csharp
// Any - returns true if any product meets condition
bool hasExpensiveProducts = context.Products.Any(p => p.Price > 1000);

// All - returns true if all products meet condition
bool allProductsHaveStock = context.Products.All(p => p.Stock > 0);

// Combined with Where
bool hasExpensiveActiveProducts = context.Products
    .Where(p => p.IsActive)
    .Any(p => p.Price > 1000);
```

## Basic Query Syntax Examples

### Basic Select with Query Syntax
Simple select using query syntax instead of method syntax.

```csharp
// Basic select
var products = from p in context.Products
               select p;

// Select specific columns
var productInfo = from p in context.Products
                  select new { p.Name, p.Price };

// Select with Where condition
var expensiveProducts = from p in context.Products
                        where p.Price > 100
                        select p;
```

### Top/Limit Results
Getting only a limited number of results.

```csharp
// Get first product (equivalent to First())
var firstProduct = (from p in context.Products 
                   select p).First();

// Get first product or null (equivalent to FirstOrDefault())
var maybeProduct = (from p in context.Products
                   where p.Id == 100
                   select p).FirstOrDefault();

// Take only 5 products (equivalent to Take(5))
var topFiveProducts = (from p in context.Products
                      select p).Take(5);

// Get the most expensive product
var mostExpensive = (from p in context.Products
                    select p).OrderByDescending(p => p.Price).First();
```

### Min and Max Examples
Finding minimum and maximum values.

```csharp
// Find cheapest price
var minPrice = (from p in context.Products
               select p.Price).Min();

// Find most expensive price
var maxPrice = (from p in context.Products
               select p.Price).Max();

// Product with minimum price
var cheapestProduct = from p in context.Products
                     where p.Price == (from p2 in context.Products select p2.Price).Min()
                     select p;
                     
// Product with maximum price
var mostExpensiveProduct = from p in context.Products
                          where p.Price == (from p2 in context.Products select p2.Price).Max()
                          select p;
```

### String Operations (LIKE equivalent)
String comparison operations similar to SQL LIKE.

```csharp
// Contains (LIKE '%text%')
var searchResults = from p in context.Products
                   where p.Name.Contains("phone")
                   select p;

// StartsWith (LIKE 'text%')
var startsWithA = from p in context.Products
                 where p.Name.StartsWith("A")
                 select p;

// EndsWith (LIKE '%text')
var endsWithModel = from p in context.Products
                   where p.Name.EndsWith("model")
                   select p;

// Case insensitive comparison (using ToLower or ToUpper)
var caseInsensitive = from p in context.Products
                     where p.Name.ToLower().Contains("iphone")
                     select p;
```

## Advanced LINQ Operations

### Nested Queries with References
Working with related data.

```csharp
// Select products with their category name
var productsWithCategoryName = context.Products
    .Select(p => new {
        ProductName = p.Name,
        CategoryName = p.Category.Name
    })
    .ToList();

// Products with specific category and supplier
var filteredProducts = context.Products
    .Where(p => p.Category.Name == "Electronics" && p.Supplier.Country == "USA")
    .ToList();

// Filter by related collection
var suppliersWithExpensiveProducts = context.Suppliers
    .Where(s => s.Products.Any(p => p.Price > 1000))
    .ToList();
```

### Complex Grouping and Aggregation
Advanced grouping scenarios.

```csharp
// Group by category name instead of id
var groupedByCategory = context.Products
    .Include(p => p.Category)
    .GroupBy(p => p.Category.Name)
    .Select(g => new {
        CategoryName = g.Key,
        ProductCount = g.Count(),
        AveragePrice = g.Average(p => p.Price)
    })
    .ToList();

// Complex grouping with filtering
var summarizedData = context.Orders
    .Where(o => o.OrderDate >= DateTime.Now.AddMonths(-3))
    .GroupBy(o => new { 
        Year = o.OrderDate.Year, 
        Month = o.OrderDate.Month,
        CustomerId = o.CustomerId
    })
    .Select(g => new {
        Year = g.Key.Year,
        Month = g.Key.Month,
        CustomerId = g.Key.CustomerId,
        OrderCount = g.Count(),
        TotalValue = g.Sum(o => o.TotalAmount)
    })
    .OrderBy(x => x.Year)
    .ThenBy(x => x.Month)
    .ToList();
```

### Joining Multiple Tables
Complex join scenarios.

```csharp
// Join three tables
var orderDetails = context.Orders
    .Include(o => o.Customer)
    .Include(o => o.OrderItems)
        .ThenInclude(oi => oi.Product)
    .Where(o => o.OrderDate >= DateTime.Now.AddDays(-30))
    .ToList();

// Explicit joins with multiple tables
var query = from o in context.Orders
            join c in context.Customers on o.CustomerId equals c.Id
            join e in context.Employees on o.EmployeeId equals e.Id
            where o.OrderDate >= DateTime.Now.AddDays(-30)
            select new { 
                OrderId = o.Id,
                CustomerName = c.Name,
                EmployeeName = e.Name,
                OrderDate = o.OrderDate,
                TotalAmount = o.TotalAmount
            };

// Left join example
var customersWithOrders = from c in context.Customers
                          join o in context.Orders
                          on c.Id equals o.CustomerId into customerOrders
                          from co in customerOrders.DefaultIfEmpty()
                          select new {
                              CustomerId = c.Id,
                              CustomerName = c.Name,
                              OrderId = co != null ? co.Id : (int?)null,
                              OrderDate = co != null ? co.OrderDate : (DateTime?)null
                          };
```

### Complex Filtering and Subqueries
Advanced filtering scenarios.

```csharp
// Subquery in Where clause
var popularProducts = context.Products
    .Where(p => context.OrderItems
                .Count(oi => oi.ProductId == p.Id) > 10)
    .ToList();

// Products that are more expensive than average
var expensiveProducts = context.Products
    .Where(p => p.Price > context.Products.Average(p2 => p2.Price))
    .ToList();

// Customers who ordered specific product
var customers = context.Customers
    .Where(c => c.Orders
                .SelectMany(o => o.OrderItems)
                .Any(oi => oi.ProductId == 1))
    .ToList();
```

### Conditional Aggregations
Complex aggregation scenarios.

```csharp
// Orders summary with conditional counts
var orderSummary = context.Orders
    .GroupBy(o => o.CustomerId)
    .Select(g => new {
        CustomerId = g.Key,
        TotalOrders = g.Count(),
        RecentOrders = g.Count(o => o.OrderDate >= DateTime.Now.AddDays(-30)),
        HighValueOrders = g.Count(o => o.TotalAmount > 1000),
        TotalSpent = g.Sum(o => o.TotalAmount)
    })
    .ToList();

// Product analysis with multiple aggregates
var productAnalysis = context.Products
    .GroupBy(p => p.CategoryId)
    .Select(g => new {
        CategoryId = g.Key,
        TotalProducts = g.Count(),
        ActiveProducts = g.Count(p => p.IsActive),
        DiscontinuedProducts = g.Count(p => !p.IsActive),
        AveragePriceActive = g.Where(p => p.IsActive)
                             .Average(p => p.Price),
        MaxPriceActive = g.Where(p => p.IsActive)
                         .Max(p => p.Price)
    })
    .ToList();
```

### Working with DTOs and Entity Transfer
Examples of using LINQ to transform data for transfer.

```csharp
// Map to DTO
var productDTOs = context.Products
    .Include(p => p.Category)
    .Select(p => new ProductDTO {
        Id = p.Id,
        Name = p.Name,
        Price = p.Price,
        CategoryName = p.Category.Name,
        InStock = p.Stock > 0
    })
    .ToList();

// Complex DTO mapping with nested data
var orderDTOs = context.Orders
    .Include(o => o.Customer)
    .Include(o => o.OrderItems)
        .ThenInclude(oi => oi.Product)
    .Select(o => new OrderDTO {
        Id = o.Id,
        OrderDate = o.OrderDate,
        CustomerName = o.Customer.Name,
        TotalAmount = o.TotalAmount,
        Items = o.OrderItems.Select(oi => new OrderItemDTO {
            ProductName = oi.Product.Name,
            Quantity = oi.Quantity,
            UnitPrice = oi.UnitPrice
        }).ToList()
    })
    .ToList();
```

## Additional Common Operations

### Working with Dates
Common date filtering scenarios.

```csharp
// Orders from today
var todayOrders = context.Orders
    .Where(o => o.OrderDate.Date == DateTime.Today)
    .ToList();

// Orders from last 30 days
var recentOrders = context.Orders
    .Where(o => o.OrderDate >= DateTime.Now.AddDays(-30))
    .ToList();

// Orders from specific month/year
var marchOrders = context.Orders
    .Where(o => o.OrderDate.Month == 3 && o.OrderDate.Year == 2025)
    .ToList();

// Orders between two dates
var dateRangeOrders = context.Orders
    .Where(o => o.OrderDate >= startDate && o.OrderDate <= endDate)
    .ToList();

// Orders BEFORE a specific date (less than)
var oldOrders = context.Orders
    .Where(o => o.OrderDate < new DateTime(2025, 1, 1))
    .ToList();

// Orders AFTER a specific date (greater than)
var newOrders = context.Orders
    .Where(o => o.OrderDate > new DateTime(2025, 4, 1))
    .ToList();

// Orders on or before a specific date (less than or equal)
var ordersUntilDate = context.Orders
    .Where(o => o.OrderDate <= new DateTime(2025, 3, 15))
    .ToList();

// Orders on or after a specific date (greater than or equal)
var ordersFromDate = context.Orders
    .Where(o => o.OrderDate >= new DateTime(2025, 3, 15))
    .ToList();

// Query syntax example for date comparison
var oldOrdersQuery = from o in context.Orders
                     where o.OrderDate < new DateTime(2025, 1, 1)
                     select o;
```

### Null Handling
Working with nullable fields.

```csharp
// Finding records with null values
var ordersWithoutShipDate = context.Orders
    .Where(o => o.ShippedDate == null)
    .ToList();

// Finding records with non-null values
var shippedOrders = context.Orders
    .Where(o => o.ShippedDate != null)
    .ToList();

// Default value if null
var shipDates = context.Orders
    .Select(o => o.ShippedDate ?? DateTime.Now)
    .ToList();

// Conditional queries with null checks
var pendingOrders = context.Orders
    .Where(o => o.ShippedDate == null && (o.Status == "Processing" || o.Status == "Confirmed"))
    .ToList();
```

### Distinct and Union Operations
Removing duplicates and combining result sets.

```csharp
// Get distinct category IDs
var distinctCategories = context.Products
    .Select(p => p.CategoryId)
    .Distinct()
    .ToList();

// Distinct based on multiple properties
var distinctProducts = context.Products
    .Select(p => new { p.CategoryId, p.SupplierId })
    .Distinct()
    .ToList();

// Union of two queries
var allCustomerContacts = context.Customers
    .Select(c => c.Email)
    .Union(context.Suppliers.Select(s => s.Email))
    .ToList();

// Union without removing duplicates
var allPhoneNumbers = context.Customers
    .Select(c => c.Phone)
    .Concat(context.Suppliers.Select(s => s.Phone))
    .ToList();
```

### Tracking vs NoTracking
Control entity tracking for performance optimization.

```csharp
// Default - with tracking (use when you'll modify the entities)
var productsWithTracking = context.Products.ToList();

// Without tracking (use for read-only scenarios - better performance)
var productsNoTracking = context.Products.AsNoTracking().ToList();

// Mixed approach
var product = context.Products
    .AsNoTracking()
    .FirstOrDefault(p => p.Id == 1);
    
// Re-attach to context if needed later
context.Products.Attach(product);
```

## Performance Tips

### Deferred Execution
LINQ queries use deferred execution, meaning they are not executed until you iterate or call a method like ToList().

```csharp
// Query is defined but not executed yet
var query = context.Products.Where(p => p.Price > 100);

// Query is executed when ToList() is called
var results = query.ToList();
```

### IQueryable vs IEnumerable
- IQueryable: Executed on the database server
- IEnumerable: Executed in memory

```csharp
// Good - filtering happens in the database
IQueryable<Product> queryable = context.Products.Where(p => p.Price > 100);

// Bad - all products are loaded, then filtered in memory
IEnumerable<Product> enumerable = context.Products.AsEnumerable().Where(p => p.Price > 100);
```

### Eager Loading vs Lazy Loading
Control when related entities are loaded.

```csharp
// Eager loading - loads related data in same query
var orders = context.Orders
    .Include(o => o.Customer)
    .Include(o => o.OrderItems)
    .ToList();

// Explicit loading - loads related data later
var order = context.Orders.Find(1);
context.Entry(order)
    .Collection(o => o.OrderItems)
    .Load();
context.Entry(order)
    .Reference(o => o.Customer)
    .Load();
```

### Compiled Queries
Improve performance by compiling frequently used queries.

```csharp
// Define a compiled query
private static readonly Func<AppDbContext, int, Product> GetProductById =
    EF.CompileQuery((AppDbContext context, int id) =>
        context.Products.FirstOrDefault(p => p.Id == id));

// Use the compiled query
var product = GetProductById(context, 1);
```
