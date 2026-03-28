```instructions
---
applyTo: "**"
---

# Workflow & Collaboration

How agents collaborate in this project. Read by all agents to understand the team workflow.

## Squad Roles

| Agent | Role | Owns |
|---|---|---|
| **Jarvis** (Orchestrator) | Coordinates, delegates, tracks progress | Execution flow |
| **Vision** (Analyst) | Validates requirements, detects conflicts | Requirement quality |
| **Tony Stark** (Planner) | Plans implementation, maintains Master Plan | Technical strategy |
| **Banner** (Coder) | Writes code, tests, implements logic | Code quality |
| **Wanda** (Designer) | Creates UI/UX, templates, styles | Visual experience |
| **Rogers** (Reviewer) | Code review, architecture compliance | Quality gate |

## Standard Workflow

```
User Request
    │
    ▼
┌─────────┐     Ambiguous/Breaking?     ┌──────────────────┐
│  Vision  │ ──── YES ──────────────────►│ BUSINESS APPROVAL │
│ Analyst  │                             │   (User decides)  │
└────┬─────┘                             └────────┬─────────┘
     │ NO (or approved)                           │
     ▼                                            │
┌──────────┐◄─────────────────────────────────────┘
│Tony Stark│
│ Planner  │──── Updates master-plan.md
└────┬─────┘
     │ Plan + Sub-tasks
     ▼
┌──────────────────────┐
│  Parallel Execution   │
│  Banner ║ Wanda       │
│  (Code) ║ (Design)    │
└────┬─────────────────┘
     │ Implementation complete
     ▼
┌──────────┐
│ Rogers   │
│ Reviewer │
└────┬─────┘
     │ ✅ Approved  │ ❌ Issues
     ▼              ▼
   Done        Loop back to
              Banner/Wanda
```

## Task Segmentation

Large tasks MUST be broken into sub-tasks:
- **Max 3-5 files per sub-task**
- **One layer per sub-task** (don't mix backend data layer with frontend)
- **Each sub-task is independently verifiable**
- **Dependencies are explicitly declared**

## Parallelization Rules

**Parallel when:**
- Different files, no overlap
- Different domains (styling vs. logic)
- No data dependencies between tasks

**Sequential when:**
- Task B needs output from Task A
- Same file modified by multiple tasks
- Frontend depends on backend API contracts
- Design approval needed before implementation

## File Conflict Prevention

- Each agent is scoped to specific files (assigned by Jarvis)
- If two tasks legitimately need the same file → run sequentially
- Component boundaries for UI: each agent works on distinct component subtrees

## Delegation Principle

Always delegate WHAT (the outcome), never HOW (the implementation):
- ✅ "Create a CRUD API for products across all layers"
- ❌ "Use GenericRepository<Product> and inject it into the service constructor"

## Master Plan

The `master-plan.md` file at the project root tracks ALL change requests:
- Every change request gets a numbered entry (CR-001, CR-002, ...)
- Status tracked: Proposed → Approved → In Progress → Done
- Sub-tasks listed with completion status
- Business decisions and approvals recorded

```
