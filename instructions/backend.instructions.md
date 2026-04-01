```instructions
---
applyTo: "**"
---

# Backend Rules

> Universal backend principles — technology-agnostic.
> For technology-specific patterns and code templates, consult the backend skills.

## Core Principles

- **Thin presentation layer**: receive request → validate → call service → return response. Nothing else.
- **Service layer = business logic owner**: all domain rules, calculations, and orchestration live here.
- **Never expose domain entities** outside the business layer — always use transfer objects (DTOs/ViewModels/etc.).
- **Depend on abstractions**: inject interfaces, not concrete implementations.
- **One service per domain aggregate** unless composition is genuinely needed.

## Error Handling
- Use typed domain exceptions (`NotFoundException`, `ValidationException`, `BusinessException`).
- Map exceptions to appropriate HTTP status codes in a single centralized handler.
- Never catch and swallow exceptions silently.
- Log full context on errors (entity ID, operation, user if applicable).

## Data Layer
- All persistence configuration via the ORM's code-first approach — never via attributes on domain entities.
- Always index foreign keys and frequently queried columns.
- One migration per logical schema change. Never modify the database manually.
- Generic data-access abstraction for standard CRUD. Specific queries only when needed.

## Validation
- Validate at the presentation layer boundary (request entry point).
- Never validate in the service layer what the presentation layer should have caught.
- Return structured, actionable validation errors.

## Testing
- Unit test: mock the data layer, test the service in isolation.
- Integration test: test the full stack from HTTP to DB.
- One assertion per test when practical. Arrange → Act → Assert.

## Skills to Consult

Before implementing, always consult the relevant skills for this project:

| Task | Skill to use |
|---|---|
| Writing controllers/endpoints | `backend-patterns` |
| Writing services and business logic | `backend-patterns` |
| Writing entities and ORM config | `backend-patterns` |
| API routes, DTOs, status codes | `api-design` |
| Unit tests and mocking | `testing-patterns` |
| Stack setup, DI registration, logging config | `backend-stack` |

```
