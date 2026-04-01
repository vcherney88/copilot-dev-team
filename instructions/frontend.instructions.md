```instructions
---
applyTo: "**"
---

# Frontend Rules

> Universal frontend principles — technology-agnostic.
> For technology-specific patterns and code templates, consult the frontend skills.

## Core Principles

- **Smart components** (containers): fetch data, connect to services, pass data down.
- **Dumb components** (presentational): receive data via props/inputs, emit events upward. No service injection.
- **Never hardcode API URLs**: use environment configuration.
- **Async data**: use the framework's reactive/async patterns — never block the UI.
- **Separation of concerns**: components handle presentation; services handle data access and business logic.

## UX Principles
- Every user action must have visible feedback (loading, success, error).
- Design for all states: empty, loading, populated, error, disabled.
- Mobile-first responsive design.
- Accessibility is mandatory: semantic markup, keyboard navigation, sufficient contrast, screen reader support.

## Form Rules
- Client-side validation with inline error messages, next to the field.
- Disable submit while form is invalid or submitting.
- Show success/error feedback after submission.

## Routing
- Lazy load feature modules/pages — don't load everything upfront.
- Protect routes with guards for authenticated/authorized sections.
- RESTful URL patterns: `/resource`, `/resource/new`, `/resource/:id`, `/resource/:id/edit`.

## Error Handling
- Handle HTTP errors centrally (interceptor/middleware), not per-service call.
- Show user-friendly messages — never expose technical error details.
- 401 → redirect to login, 403 → forbidden page, 500 → generic error.

## Component Quality Checklist
- [ ] All interactive states handled (hover, focus, active, disabled)
- [ ] Error states with actionable messages
- [ ] Empty states with helpful guidance
- [ ] Loading states with appropriate indicators
- [ ] Responsive across breakpoints
- [ ] Keyboard navigable
- [ ] Accessible labels and structure (WCAG AA)

## Skills to Consult

Before implementing, always consult the relevant skills for this project:

| Task | Skill to use |
|---|---|
| Writing components (smart and dumb) | `frontend-patterns` |
| Writing HTTP services | `frontend-patterns` |
| Routing configuration | `frontend-patterns` |
| Forms and validation | `frontend-patterns` |
| HTTP interceptors | `frontend-patterns` |
| Component and service tests | `testing-patterns` |
| Framework setup, environment config | `frontend-stack` |

```
