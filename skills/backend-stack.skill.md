```skill
---
name: Backend Stack
description: Technology-specific stack details for the backend. Consult this skill for framework version, ORM configuration, DI setup, logging library, naming conventions per language, and folder structure.
---

# Backend Stack — .NET Core 10 + PostgreSQL

> This skill contains ALL technology-specific backend details for this project.
> To migrate to a different backend stack (e.g., Java Spring, Node.js NestJS), replace this skill file.

## Technology Stack

| Component | Technology | Version |
|---|---|---|
| Backend framework | .NET Core | 10 |
| Database | PostgreSQL | latest |
| ORM | Entity Framework Core (Npgsql) | latest compatible |
| Logging | Serilog → PostgreSQL | latest |
| Caching | IMemoryCache (in-process) | built-in |
| Testing | xUnit + Moq | latest |
| Validation | Data Annotations or FluentValidation | per feature |
| API docs | Swagger / Scalar | built-in |

## Folder Structure

```
src/
  Api/
    Controllers/
    DTOs/
    Validators/
  Business/
    Services/
    Exceptions/
  Data/
    Entities/
    Configurations/
    Repositories/
    Migrations/
    AppDbContext.cs
  Program.cs
```

## Naming Conventions

| Scope | Convention | Example |
|---|---|---|
| C# public members | PascalCase | `GetProductById` |
| C# private fields | `_camelCase` | `_productService` |
| C# interfaces | `I` prefix | `IProductService` |
| C# async methods | `Async` suffix | `GetByIdAsync` |
| C# exception classes | `Exception` suffix | `NotFoundException` |
| C# test classes | `Tests` suffix | `ProductServiceTests` |
| C# test methods | `Method_Scenario_Result` | `GetByIdAsync_NotFound_Throws` |
| DB tables | snake_case plural | `products`, `order_items` |
| DB columns | snake_case | `created_at`, `category_id` |
| DTOs | Suffix by use | `ProductDto`, `CreateProductRequest`, `UpdateProductRequest` |
| Migration names | PascalCase descriptive | `AddProductsTable`, `AddCategoryIndex` |

## DI Registration Pattern (Program.cs)

```csharp
// Database
builder.Services.AddDbContext<AppDbContext>(o =>
    o.UseNpgsql(builder.Configuration.GetConnectionString("Default")));

// Repositories
builder.Services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
builder.Services.AddScoped<IProductRepository, ProductRepository>(); // specific only if needed

// Services
builder.Services.AddScoped<IProductService, ProductService>();

// Logging (Serilog)
builder.Host.UseSerilog((ctx, cfg) =>
    cfg.ReadFrom.Configuration(ctx.Configuration));
```

## Logging Pattern (Serilog)

```csharp
// Structured logging — always use message templates, never string interpolation
_logger.LogInformation("Processing order {OrderId} for user {UserId}", orderId, userId);
_logger.LogWarning("Product {ProductId} stock low: {Stock}", productId, stock);
_logger.LogError(ex, "Failed to create product {ProductName}", request.Name);
```

- Log sink: PostgreSQL (separate connection string `Logging`)
- Create log table via SQL script (`IF NOT EXISTS`) — not via EF migrations
- Never log sensitive data (passwords, tokens, PII)

## Caching Pattern (IMemoryCache)

```csharp
public async Task<IEnumerable<CategoryDto>> GetCategoriesAsync()
{
    return await _cache.GetOrCreateAsync("categories", async entry =>
    {
        entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(15);
        return await _repository.GetAllAsync();
    });
}

// Invalidate on write
_cache.Remove("categories");
```

- Use for: lookup data, categories, configuration tables (rarely changing)
- TTL: 5-30 minutes depending on volatility
- Never cache: user-specific data, cart, session, financial data

## Error Handling Pattern

```csharp
// Custom exceptions
public class NotFoundException : Exception
{
    public NotFoundException(string entity, object id)
        : base($"{entity} with id {id} was not found.") { }
}

public class BusinessException : Exception
{
    public BusinessException(string message) : base(message) { }
}

// Global exception middleware in Program.cs
app.UseExceptionHandler(appBuilder => appBuilder.Run(async ctx =>
{
    var exception = ctx.Features.Get<IExceptionHandlerFeature>()?.Error;
    var (status, message) = exception switch
    {
        NotFoundException => (404, exception.Message),
        BusinessException => (400, exception.Message),
        _ => (500, "An unexpected error occurred.")
    };
    ctx.Response.StatusCode = status;
    await ctx.Response.WriteAsJsonAsync(new { error = message });
}));
```

## Authentication Pattern

```csharp
// JWT Bearer (adjust to project auth strategy)
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => { /* config */ });

// Controller level
[Authorize]
[Authorize(Roles = "Admin")]
[AllowAnonymous]
```

## Data Seeding

```
// Schema → EF Core migrations only
dotnet ef migrations add MigrationName
dotnet ef database update

// Reference data → idempotent SQL scripts
INSERT INTO categories (id, name) VALUES (1, 'Electronics')
ON CONFLICT (id) DO NOTHING;
```

```
