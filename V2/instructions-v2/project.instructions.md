---
applyTo: "**"
---

# Project Standards & Architecture

## TDD
- Red → Green → Refactor. Always.
- Write the test first. No code without a failing test.
- Test behavior (what), not implementation (how).

## Clean Code
- One function = one task. Max ~20 lines. Max 2 levels of nesting.
- Extract try/catch and loop bodies to dedicated methods.
- No magic numbers — use named constants.
- No comments explaining WHAT — only WHY (when non-obvious).
- Structured logging at all system boundaries. Never expose internal exception details to the client.

## Architecture
- Strict layer flow: Presentation → Business → Data. Never skip or reverse.
- Business layer owns all logic. Never in controllers, never in repositories.
- Depend on abstractions (interfaces), never concrete implementations.
- Infrastructure code (DB, external APIs, email) lives in the Data layer only.
- No domain entities exposed to the Presentation layer — always use DTOs/ViewModels.

## Modification Rules
- Always search for existing patterns before creating new ones.
- Skill files drive all technology-specific decisions — consult them before implementing.
- Agents do NOT modify `instructions/` or `skills/` — only Tony Stark (Architect) does.
- When a new pattern is established, Tony Stark updates the relevant skill file.

## Agent Squad
| Agent | Role | When to call |
|-------|------|--------------|
| Pepper Potts | Orchestrator | Entry point — always |
| Vision | Analyst | New features, ambiguous requests, conflicts |
| Tony Stark | Architect | Tech decisions, new integrations, reverse engineering |
| Nick Fury | Planner | After analysis — creates segmented implementation plan |
| Banner | Coder | Implementation |
| Wanda | Designer | UI/UX, templates, styles |
| Rogers | Reviewer | Post-implementation quality gate |

**Flow**: User → Pepper → [Vision?] → [Tony Stark?] → Nick Fury → Banner/Wanda (parallel) → Rogers → User

## Skills Registry
All project-specific technology details live exclusively in skills:
- `backend-stack` — framework, database, logging, naming, folder structure
- `frontend-stack` — framework, env config, naming, template syntax
- `backend-patterns` — code templates for backend layers
- `frontend-patterns` — code templates for frontend components
- `testing-patterns` — unit and integration test templates
- `api-design` — routes, HTTP verbs, status codes, DTOs
