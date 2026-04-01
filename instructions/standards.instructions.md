```instructions
---
applyTo: "**"
---

# Standards & Workflow

Generic coding standards and agent collaboration rules. Applies to all projects regardless of stack.

---

## 1. Test Driven Development

Red-Green-Refactor cycle for every change:
1. **Red** — failing test that defines the behavior
2. **Green** — minimum code to pass
3. **Refactor** — simplify, remove duplication

## 2. Clean Code

### Functions
- One function = one task. Max ~20 lines.
- Linear control flow, max 2 nesting levels.
- Extract: loop bodies, try-catch blocks, complex conditions → separate methods.
- Pass state explicitly. No globals.

### Naming
- **Descriptive, intent-revealing.** No abbreviations (except `id`, `url`, `dto`).
- Booleans: `is`, `has`, `can`, `should` prefix.
- Methods: verbs (`getUser`, `calculateTotal`).
- Classes: nouns (`ProductService`, `OrderRepository`).
- Project-specific casing rules in `architecture.instructions.md`.

### Comments
- Avoid. Code should be self-documenting.
- Only for: invariants, assumptions, external requirements, non-obvious WHY.
- Delete commented-out code.

### Error Handling
- Typed/domain exceptions, not generic `Exception`.
- Error handling = dedicated methods (not inline).
- Never swallow exceptions silently.

### Logging
- Structured, at service boundaries and errors.
- Levels: Debug (dev), Info (business events), Warn (recoverable), Error (failures).
- Include context: entity IDs, operation names.
- Project-specific sink/library in `architecture.instructions.md`.

## 3. Architecture Principles

- Each layer has a single responsibility. Never skip layers.
- Upper layers depend on lower, never reverse.
- Domain entities never exposed to presentation layer.
- Depend on abstractions (interfaces), not implementations.
- Code should be regenerable — any file rewritable without breaking the system.

## 4. Modification Rules

- Follow existing codebase patterns.
- Search for similar code BEFORE creating new patterns.
- Prefer full-file rewrites over micro-edits (AI-friendly).
- One logical change per commit.

---

## 5. Agent Workflow

### Squad

| Agent | Role |
|---|---|
| **Pepper** | Orchestrator — coordinates, delegates, never codes |
| **Vision** | Analyst — validates requirements, detects conflicts, business gate |
| **Tony Stark** | Architect — evaluates complex tech decisions, proposes architecture, evolves instructions and skills |
| **Nick Fury** | Planner — plans implementation, maintains `master-plan.md` |
| **Banner** | Coder — writes code, TDD, clean code |
| **Wanda** | Designer — UI/UX, templates, styles |
| **Rogers** | Reviewer — code review, quality gate |

### Flow

```
Request → Vision (if needed) → [Tony Stark if complex] → Nick Fury → Banner + Wanda → Rogers → Done
```

- Vision escalates ambiguous/breaking changes → **user must approve** before proceeding.
- Tony Stark steps in only for architecture decisions, tech choices, or instruction/skill evolution.
- Nick Fury segments large tasks: **max 3-5 files per sub-task, one layer at a time**.
- Nick Fury updates `master-plan.md` with every CR (numbered CR-001, CR-002...).

### Skills (on-demand knowledge)

Skills are detailed reference documents that agents consult when needed. They do NOT consume context until invoked.

**Generic skills** (reusable across projects — never replace these):
> None currently — all skills are project-specific.

**Project-specific skills** (replace these when switching projects):
- **backend-stack** — Technology stack details: framework, ORM, DI setup, logging, naming conventions, folder structure
- **frontend-stack** — Technology stack details: framework, environment config, naming conventions, folder structure
- **backend-patterns** — Backend code templates: controller, service, entity, repository, ORM config
- **frontend-patterns** — Frontend code templates: component, service, routing, forms, interceptor
- **testing-patterns** — Unit test, integration test, and mocking templates
- **api-design** — REST API conventions, status codes, pagination, DTOs

Agents must consult the relevant skills **before implementing** to ensure technology consistency.

### Parallelization

**Parallel:** different files, different domains, no data dependencies.
**Sequential:** same file, cross-layer dependency, frontend depends on backend API.

### Delegation

Describe WHAT (outcome), never HOW (implementation).

```
