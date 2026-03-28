```instructions
---
applyTo: "**"
---

# Cross-Cutting Concerns

Project-specific rules for logging, caching, error handling, authentication, and other cross-cutting concerns.

> **⚠️ TEMPLATE**: Customize this file for each project. The examples below are from a .NET + PostgreSQL stack. Replace with your project's actual conventions.

## Logging

- **Library**: Serilog
- **Sink**: PostgreSQL database (no file logging)
- **Connection**: Use a SEPARATE connection string for logging (not the main app DB connection)
- **Setup**: Create a `.sql` seed script for the log table (`IF NOT EXISTS`)
- **Structured logging**: Use message templates with named parameters
  ```
  _logger.LogInformation("Processing order {OrderId} for customer {CustomerId}", orderId, customerId);
  ```
- **Log levels**:
  - `Debug` — development-time detail (disabled in production)
  - `Information` — business events (order created, user logged in)
  - `Warning` — recoverable issues (retry succeeded, fallback used)
  - `Error` — failures requiring attention (unhandled exception, external service down)

## Caching

- **Strategy**: In-memory caching (`IMemoryCache` or equivalent)
- **Use for**: Frequently accessed data that rarely changes (e.g., categories, lookup tables, configuration)
- **Do NOT cache**: User-specific data, frequently changing data, large datasets
- **Invalidation**: Invalidate cache on write operations that affect cached data
- **TTL**: Set appropriate expiration (e.g., 5-30 minutes for reference data)

## Error Handling

- **Global middleware** catches unhandled exceptions and maps them to appropriate HTTP responses
- **Typed domain exceptions** for business errors:
  - `NotFoundException` → 404
  - `BusinessException` → 400 / 409
  - `UnauthorizedException` → 401
  - `ForbiddenException` → 403
- **Never expose stack traces** in production API responses
- **Always log the full exception** server-side

## Authentication & Authorization

- Define auth strategy per project (JWT Bearer, Cookie, OAuth, etc.)
- Protect endpoints at controller/action level
- Role-based or policy-based authorization as needed
- Public endpoints marked explicitly

## Data Seeding

- **Structure** (schema): Managed through ORM migrations only
- **Data** (seed/reference data): Managed through `.sql` seed scripts
- Never mix structural migrations with data seeding
- Seed scripts must be idempotent (safe to run multiple times)

```
