```instructions
---
applyTo: "**"
---

# Coding Standards

These standards apply to ALL code in the project, regardless of language or framework.

## Test Driven Development

Follow the Red-Green-Refactor cycle:
1. **Red** — Write a failing test that defines a specific behavior
2. **Green** — Write the minimum code to make the test pass
3. **Refactor** — Improve clarity, remove duplication, simplify

Every new feature or bug fix starts with a test.

## Clean Code Principles

### Functions
- Small and focused — each function does ONE thing
- Max 20 lines as a guideline (not a hard rule)
- Linear control flow — avoid deep nesting (max 2 levels)
- Extract loop bodies into separate methods
- Extract try-catch into dedicated error-handling methods
- Pass state explicitly — no globals, no hidden dependencies

### Naming
- **Descriptive and intent-revealing** — names should explain what and why
- No abbreviations unless universally understood (e.g., `id`, `url`, `dto`)
- Boolean variables/methods: use `is`, `has`, `can`, `should` prefixes
- Methods: use verbs (`getUser`, `calculateTotal`, `validateInput`)
- Classes: use nouns (`ProductService`, `OrderRepository`)
- Follow project-specific naming conventions in `architecture.instructions.md`

### Comments
- **Avoid comments** — write self-documenting code instead
- Only comment: invariants, assumptions, external requirements, or non-obvious WHY
- Never comment WHAT the code does — make the code readable
- Delete commented-out code — use version control instead

### Error Handling
- Errors are first-class citizens — handle them explicitly
- Use typed/domain exceptions — not generic `Exception`
- Error handling is a separate concern — dedicated methods
- Error messages must be actionable (user-facing) or informative (logs)
- Never swallow exceptions silently

### Logging
- Structured logging at key boundaries (entry/exit of services, errors)
- Use log levels correctly: Debug (dev detail), Info (business events), Warn (recoverable), Error (failures)
- Include context: entity IDs, operation names, correlation IDs
- Follow project-specific logging conventions in `cross-cutting.instructions.md`

## Architecture Principles

### Layer Separation
- Each layer has a single responsibility
- Never skip layers in the dependency flow
- Upper layers depend on lower layers, never the reverse
- Entities/domain models are never exposed to the presentation layer

### Coupling and Cohesion
- Minimize coupling — files should be replaceable without cascading changes
- Maximize cohesion — related code lives together
- Depend on abstractions (interfaces), not concrete implementations
- Group by feature/domain, not by technical role (when possible)

### Regenerability
- Write code so any file/module can be rewritten from scratch
- Keep configuration declarative (JSON, YAML, etc.)
- Prefer convention over configuration

## Modification Rules

- When extending/refactoring: follow existing patterns in the codebase
- Search for similar implementations BEFORE creating new patterns
- Prefer full-file rewrites over micro-edits (easier for AI agents)
- One logical change per commit

## Quality Checklist

- [ ] Tests written BEFORE implementation (TDD)
- [ ] All tests pass
- [ ] No code duplication requiring the same fix in multiple places
- [ ] Functions are small and focused
- [ ] Naming is descriptive and consistent
- [ ] Error handling is explicit
- [ ] Logging follows project conventions
- [ ] Layer boundaries respected
- [ ] No hardcoded secrets or magic numbers

```
