---
applyTo: "**"
---

# Code Rules

## Backend
- Controllers are thin: parse input, call service, return response. No logic.
- Services own all business logic. One service per bounded domain area.
- Repositories own all data access. Services never write raw queries.
- Validate at the API boundary. Services assume validated input.
- Centralized error handling — no try/catch scattered across controllers.

### Skills to consult for backend work
| Task | Skill |
|------|-------|
| Setting up a new project or layer | `backend-stack` |
| Creating controllers, services, entities, repositories | `backend-patterns` |
| Writing unit or integration tests | `testing-patterns` |
| Designing REST endpoints and DTOs | `api-design` |

## Frontend
- Components are pure presenters. Business logic belongs in services.
- Services own HTTP calls and state. Components only consume services.
- Reactive patterns preferred — observables/signals over imperative subscriptions.
- Validate forms at the form level, show errors at the field level.
- Route guards enforce access control — never inline auth checks in templates.

### Component checklist
- ✅ Handles all states: empty, loading, populated, error, disabled
- ✅ All inputs typed (no `any`)
- ✅ Unsubscribe / use async patterns — no memory leaks
- ✅ Accessible: semantic markup, ARIA labels, keyboard navigation

### Skills to consult for frontend work
| Task | Skill |
|------|-------|
| Setting up a new feature or module | `frontend-stack` |
| Creating components, services, routing, forms | `frontend-patterns` |
| Writing component or service tests | `testing-patterns` |
| Designing API contracts consumed by the frontend | `api-design` |
