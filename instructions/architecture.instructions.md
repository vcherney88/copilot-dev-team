```instructions
---
applyTo: "**"
---

# Project Architecture

> **Customize this file per project.** Stack, layers, naming, cross-cutting concerns.

## Stack

- **Backend**: .NET Core 10
- **Database**: PostgreSQL
- **Frontend**: Angular
- **ORM**: Entity Framework Core (Npgsql)

## Layer Structure

```
API Layer       → Controllers, DTOs, Validation, Swagger
Business Layer  → Services (interface + implementation), domain logic
Data Layer      → Entities, DbContext, Configurations, Repositories, Migrations
```

**Dependency flow:** Frontend → API → Business → Data → DB. Never skip layers, never reference upward.

## Folder Structure

### Backend
```
src/
  Api/Controllers/, DTOs/, Validators/
  Business/Services/, Exceptions/
  Data/Entities/, Configurations/, Repositories/, Migrations/, AppDbContext.cs
  Program.cs
```

### Frontend (Angular)
```
src/app/
  core/services/, interceptors/, guards/
  shared/components/, pipes/, directives/
  features/{feature}/components/, services/, models/, {feature}.module.ts
```

## Repository Pattern

- **Generic** `IRepository<T>` / `Repository<T>` for standard CRUD (`GetByIdAsync`, `GetAllAsync`, `AddAsync`, `UpdateAsync`, `DeleteAsync`).
- **Specific** `IXxxRepository : IRepository<Xxx>` ONLY when custom queries are needed (joins, aggregations, raw SQL).
- Don't create empty specific repositories for simple CRUD.
- Use `IUnitOfWork` (if present) for multi-repository transactions.

## API Conventions

- Routes: plural nouns `/api/products`, `/api/orders`
- Methods: GET (read), POST (create), PUT (full update), PATCH (partial), DELETE
- Status codes: 200, 201, 204, 400, 404, 409
- Pagination: `?page=1&pageSize=20`
- Always return DTOs, never entities

## Naming Conventions

| Scope | Convention | Example |
|---|---|---|
| C# public members | PascalCase | `GetProductById` |
| C# private fields | _camelCase | `_productService` |
| C# interfaces | I prefix | `IProductService` |
| C# async methods | Async suffix | `GetByIdAsync` |
| TS variables/methods | camelCase | `getProducts()` |
| TS classes/interfaces | PascalCase | `ProductService` |
| Angular files | kebab-case | `product-list.component.ts` |
| DB tables | snake_case plural | `products`, `order_items` |
| DB columns | snake_case | `created_at`, `category_id` |
| DTOs | Suffix by use | `ProductDto`, `CreateProductRequest`, `UpdateProductRequest` |
| Test methods (C#) | Method_Scenario_Result | `GetByIdAsync_NotFound_Throws` |
| Test methods (TS) | should...when... | `should return products when API succeeds` |

## Cross-Cutting Concerns

### Logging
- **Serilog** → PostgreSQL (no file logging)
- Separate connection string for log DB
- Create `.sql` script for log table (`IF NOT EXISTS`)
- Structured: `_logger.LogInformation("Processing {OrderId}", orderId)`

### Caching
- `IMemoryCache` for semi-static data (categories, lookups). TTL 5-30 min.
- Invalidate on writes. Never cache user-specific or frequently changing data.

### Error Handling
- Global exception middleware maps exceptions → HTTP status codes
- Typed exceptions: `NotFoundException` → 404, `BusinessException` → 400/409
- Never expose stack traces in production. Always log full exception server-side.

### Authentication
- `[Authorize]` at controller/action level. `[AllowAnonymous]` for public.
- Role-based: `[Authorize(Roles = "Admin")]`
- Strategy per project (JWT, Cookie, OAuth).

### Data Seeding
- Schema: ORM migrations only.
- Data: `.sql` seed scripts (idempotent, safe to re-run).
- Never mix migrations with data seeding.

```
