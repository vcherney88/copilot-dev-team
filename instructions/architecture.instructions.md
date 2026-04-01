```instructions
---
applyTo: "**"
---

# Project Architecture

> This is the project identity card. It defines structure and rules — NOT technology details.
> Technology-specific stack, patterns, and code templates are in `skills/`.
> To adapt this team to a new project, replace the skills — this file stays mostly the same.

## Stack

> See `skills/stack.skill.md` for the full technology list, versions, and configuration.

- **Backend stack**: defined in `backend-*` skills
- **Frontend stack**: defined in `frontend-*` skills
- **Database & ORM**: defined in `backend-stack.skill.md`

## Layer Structure

```
Presentation Layer  → Handles requests/responses, input validation, API contracts
Business Layer      → All domain logic, business rules, orchestration
Data Layer          → Persistence, queries, migrations, ORM configuration
```

**Dependency flow:** Presentation → Business → Data → Storage. Never skip layers, never reference upward.

## Folder Structure

> Exact folder paths are defined in `skills/backend-stack.skill.md` and `skills/frontend-stack.skill.md`.

The general layout follows this pattern:
```
src/
  [presentation-layer]/   ← framework-specific: controllers, pages, routes, DTOs
  [business-layer]/       ← services, use cases, domain logic, exceptions
  [data-layer]/           ← entities/models, repositories, migrations, ORM config
```

## Key Architectural Patterns

- **Thin presentation layer**: input validation, call service, return response. No business logic.
- **Service layer owns all business logic**: one service per domain aggregate.
- **Repository/data-access abstraction**: presentation never talks to persistence directly.
- **Transfer objects**: domain models never exposed outside the business layer.
- **Dependency inversion**: depend on abstractions (interfaces), not implementations.

## Naming Conventions

> Technology-specific casing rules (PascalCase, camelCase, snake_case per language) are in `skills/`.

| Concept | Convention |
|---|---|
| Collection endpoints | Plural nouns (`/products`, `/orders`) |
| Boolean variables | `is`, `has`, `can`, `should` prefix |
| Methods | Verbs (`getUser`, `calculateTotal`, `processOrder`) |
| Classes/Types | Nouns (`ProductService`, `OrderRepository`) |
| Request models | `Create[X]Request`, `Update[X]Request` |
| Response models | `[X]Dto`, `[X]Response`, `[X]Summary` |
| Test methods | Describe behavior: `[method]_[scenario]_[result]` or `should [behavior] when [condition]` |

## Cross-Cutting Concerns

> Implementation details (library names, config syntax) are in `skills/backend-stack.skill.md`.

- **Logging**: structured logging at service boundaries. Include entity IDs and operation context. Never log secrets.
- **Caching**: cache semi-static/lookup data. Invalidate on write. Never cache user-specific or volatile data.
- **Error Handling**: domain exceptions mapped to HTTP status codes via centralized handler. Never expose stack traces.
- **Authentication/Authorization**: access control enforced at presentation layer entry points.
- **Data Seeding**: schema via migrations only. Reference data via idempotent seed scripts.

> 📖 For technology-specific implementation: consult `skills/backend-stack.skill.md`, `skills/frontend-stack.skill.md`, `skills/backend-patterns.skill.md`, `skills/frontend-patterns.skill.md`.

```
