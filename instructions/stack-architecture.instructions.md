---
applyTo: "**"
---

# Project Architecture

## Stack
- **Backend**: .NET Core 10
- **Database**: PostgreSQL
- **Frontend**: Angular
- **ORM**: Entity Framework Core with Npgsql provider

## Multi-Layer Architecture (Backend)

```
API Layer          → Controllers, DTOs, Validation, Auth, Swagger
Business Layer     → Services (interfaces + implementations), domain logic
Data Layer         → Entities, EF Core DbContext, Configurations, Repositories, Migrations
```

### Layer Responsibilities
- **Controllers** are thin: validate input → call service → return HTTP response. No business logic.
- **Services** contain ALL business rules and orchestration logic.
- **Repositories** contain ONLY data access. No business rules, no HTTP concerns.
- **Entities** are never exposed to the API layer. Always map to DTOs.

## Dependency Flow

```
Angular Component
  → Angular Service (HttpClient)
    → .NET Controller (API Layer)
      → .NET Service (Business Layer)
        → Repository (Data Layer)
          → EF Core DbContext
            → PostgreSQL
```

**Frontend always depends on backend API** — never implement Angular features before the API contract is defined.

## Repository Pattern

### Generic Repository
A single `IRepository<T>` / `Repository<T>` provides standard CRUD for all entities:
- `GetByIdAsync(id)`
- `GetAllAsync()`
- `AddAsync(entity)`
- `UpdateAsync(entity)`
- `DeleteAsync(id)`

### Specific Repositories
Create `IProductRepository : IRepository<Product>` ONLY when you need:
- Custom queries not covered by CRUD (e.g., `GetByCategoryAsync`, `SearchAsync`)
- Complex LINQ with joins, projections, or aggregations
- Raw SQL or stored procedure calls

**For simple CRUD entities: use the generic repository directly. Do NOT create an empty specific repository.**

### Unit of Work
If `IUnitOfWork` exists in the codebase, use it for transactional operations spanning multiple repositories.

## Folder Structure

### Backend
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

### Frontend (Angular)
```
src/app/
  core/              ← singleton services, interceptors, guards
  shared/            ← reusable components, pipes, directives
  features/
    product/
      components/
      services/
      models/
      product-routing.module.ts
      product.module.ts
```

## API Conventions
- Resource naming: plural nouns → `/api/products`, `/api/orders`
- HTTP methods: GET (read), POST (create), PUT (full update), PATCH (partial), DELETE
- Status codes: 200 OK, 201 Created, 204 NoContent, 400 BadRequest, 404 NotFound, 409 Conflict
- Pagination: `?page=1&pageSize=20`
- Always return DTOs, never EF Core entities

## Naming Conventions

| Concern | Convention | Example |
|---|---|---|
| C# public members | PascalCase | `GetProductById` |
| C# private fields | `_camelCase` | `_productService` |
| C# interfaces | `I` prefix | `IProductService` |
| C# async methods | `Async` suffix | `GetByIdAsync` |
| TS variables/methods | camelCase | `getProducts()` |
| TS classes/interfaces | PascalCase | `ProductService` |
| Angular file names | kebab-case | `product-list.component.ts` |
| DB table names | snake_case plural | `products`, `order_items` |
| DB column names | snake_case | `created_at`, `category_id` |

## Cache
use in memory caching (e.g., `IMemoryCache`) for frequently accessed data that rarely changes (e.g., product categories). 
## Data Seed
data only into file .sql seed scripts 
Mogration for the structure
## Logging
  Serilog into database no files, create .sqlscript for the table  if not exists. use separate db connection string for log




