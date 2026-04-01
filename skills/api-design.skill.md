```skill
---
name: API Design Patterns
description: Reference for REST API conventions, status codes, pagination, DTOs, and error responses. Consult this skill when designing or implementing API endpoints.
---

# API Design Patterns

> Adapt to the conventions defined in `architecture.instructions.md`. These are general REST best practices.

## Route Design

| Pattern | Example | Notes |
|---|---|---|
| Collection | `GET /api/products` | Plural nouns |
| Single item | `GET /api/products/{id}` | Path parameter |
| Nested resource | `GET /api/orders/{id}/items` | Only 1 level deep |
| Action (rare) | `POST /api/orders/{id}/cancel` | When CRUD doesn't fit |

## HTTP Methods & Status Codes

| Method | Purpose | Success | Error Cases |
|---|---|---|---|
| `GET` | Read | `200 OK` | `404 Not Found` |
| `POST` | Create | `201 Created` | `400 Bad Request`, `409 Conflict` |
| `PUT` | Full update | `200 OK` or `204 No Content` | `400`, `404` |
| `PATCH` | Partial update | `200 OK` or `204 No Content` | `400`, `404` |
| `DELETE` | Remove | `204 No Content` | `404` |

## Pagination

```
GET /api/products?page=1&pageSize=20

Response:
{
  "items": [...],
  "totalCount": 150,
  "page": 1,
  "pageSize": 20,
  "totalPages": 8
}
```

## DTO Naming Convention

| Purpose | Suffix | Example |
|---|---|---|
| Read/Response | `Dto` | `ProductDto` |
| Create request | `CreateXxxRequest` | `CreateProductRequest` |
| Update request | `UpdateXxxRequest` | `UpdateProductRequest` |
| List item (lightweight) | `XxxSummaryDto` | `ProductSummaryDto` |
| Filter/Query | `XxxFilter` | `ProductFilter` |

## Error Response Shape

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "Validation Error",
  "status": 400,
  "errors": {
    "name": ["Name is required"],
    "price": ["Price must be greater than 0"]
  }
}
```

## Versioning (if applicable)

- URL prefix: `/api/v1/products` (simple, explicit)
- Header: `Api-Version: 1.0` (cleaner URLs)
- Choose one strategy and be consistent.

## Security Checklist

- `[Authorize]` or equivalent on all non-public endpoints
- Input validation on all request bodies
- Parameterized queries (never string concatenation for SQL)
- Rate limiting on public endpoints
- CORS configuration for frontend origin

```
