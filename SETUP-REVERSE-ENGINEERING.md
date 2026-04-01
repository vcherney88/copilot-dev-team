# Setup Guide — Integrating the Squad into an Existing Project

## New Project (no existing code)

1. Copy `agents/`, `instructions/`, `skills/` into the project root
2. Open the project in VS Code
3. Run the **Reverse Engineering Prompt** below — it will generate the project-specific skill files
4. Place the generated files into `skills/`
5. Done — the Squad is ready

---

## Existing Project — Reverse Engineering Prompt

> **How to use:**
> 1. Open Copilot Chat (or any AI agent) in the context of your existing project
> 2. Copy and paste the entire prompt block below
> 3. The AI will analyze the codebase and generate the 6 skill files
> 4. Copy the generated files into the `skills/` folder of your project

---

```
You are a Senior Software Architect performing a reverse engineering analysis on this codebase.

Your goal is to generate 6 skill files that the AI coding squad will use as on-demand references.
Analyze the ACTUAL code — never invent or assume patterns. Everything must be derived from what exists.

---

## STEP 1 — Detect the stack

Read the following files to identify technologies:
- *.csproj → .NET version (<TargetFramework>)
- package.json → Node.js / frontend framework (check "dependencies" and "devDependencies")
- pom.xml or build.gradle → Java / Spring Boot
- requirements.txt or pyproject.toml → Python
- go.mod → Go
- angular.json → confirms Angular
- vite.config.* or next.config.* → React / Vue / Next.js
- pubspec.yaml → Flutter
- appsettings.json or .env or docker-compose.yml → database type and connection strings

Extract: backend language + framework + version, frontend framework + version, database, ORM, test libraries.

---

## STEP 2 — Map the folder structure

List the directory tree (first 4 levels, excluding node_modules, bin, obj, .git).
Identify which folders belong to each architectural layer:
- Presentation layer: Controllers, Endpoints, Pages, Routes, Routers
- Business layer: Services, UseCases, Handlers, Commands, Application
- Data layer: Repositories, Infrastructure, Persistence, Data, Migrations
- Domain: Entities, Models, Aggregates, Domain
- Frontend features: features/, pages/, screens/, views/, modules/
- Frontend shared: shared/, core/, components/, common/

Record the actual folder paths.

---

## STEP 3 — Extract naming conventions

Sample 15–20 real source files across all layers (not tests, not generated files).
For each, read it and extract:
- Class/type naming casing
- Method naming casing
- Private field naming (prefix? underscore?)
- File naming pattern (kebab-case, PascalCase, snake_case?)
- Interface naming (I prefix or not?)
- Async method convention (Async suffix or not?)
- DB table and column naming (check ORM config or migration files)
- DTO/model naming suffix pattern (Dto, Request, Response, ViewModel, Model?)
- Test method naming (read one test file)

---

## STEP 4 — Extract architectural patterns

Search the codebase for:
- Repository pattern: IRepository, Repository<T>, GenericRepository, BaseRepository
- Unit of Work: IUnitOfWork, UnitOfWork
- DI registration: AddScoped/AddTransient/AddSingleton (C#), @Injectable/@Bean, providedIn
- Transfer objects: files named *Dto*, *Request*, *Response*, *ViewModel*
- Custom exceptions: files named *Exception*, *Error*
- Read the app entry point (Program.cs, Startup.cs, main.ts, app.module.ts, main.py) for wiring

---

## STEP 5 — Extract cross-cutting concerns

Search and read actual implementations of:
- Logging library (Serilog, NLog, Winston, ILogger, logback, etc.) — find actual log calls
- Caching (IMemoryCache, Redis, MemoryCache) — find actual cache usage in services
- Auth (attributes/decorators/middleware: [Authorize], @UseGuards, @Secured, JWT setup)
- Global error handler (middleware, exception filter, ControllerAdvice)
- Validation library (FluentValidation, DataAnnotations, class-validator, Zod, Yup)

---

## STEP 6 — Extract backend code templates

Find the cleanest, most complete real example of each:
1. A typical controller/endpoint handler (with GET + POST at minimum)
2. A service — interface + implementation
3. A domain entity/model with relationships
4. ORM configuration (Fluent API, entity decorators, mapping config)
5. A generic repository and one specific repository (if exists)
6. The DI registration block from the entry point
7. Custom exception classes

Strip business-specific names — rename entities to Product/Order as generic examples.

---

## STEP 7 — Extract frontend code templates

Find the cleanest real example of each:
1. A smart (container) component that fetches data and uses a service
2. A dumb (presentational) component with only inputs/props
3. An HTTP service that calls the backend
4. A model/interface definition (DTO)
5. Routing configuration (with lazy loading if present)
6. A reactive/controlled form with validation
7. An HTTP interceptor (error handling or auth header)

Strip business-specific names — use Product as generic example.

---

## STEP 8 — Extract testing patterns

Find real test file examples of:
1. A backend unit test (service tested in isolation with mocking)
2. A backend integration test (full HTTP request to endpoint)
3. A frontend component test
4. A frontend service/HTTP test

Record: test framework, mocking library, setup pattern, assertion style.

---

## OUTPUT — Generate these 6 files

### FILE 1: `backend-stack.skill.md`

```skill
---
name: Backend Stack
description: Technology-specific stack details for the backend. Consult this skill for framework version, DI setup, logging config, naming conventions per language, folder structure, and migration commands.
---

# Backend Stack — [detected framework + version]

## Technology Stack
| Component | Technology | Version |
[fill from Step 1]

## Folder Structure
[fill from Step 2 — actual paths]

## Naming Conventions
| Scope | Convention | Example |
[fill from Step 3 — real examples from code]

## DI Registration Pattern
[fill from Step 5 — actual registration code from entry point]

## Logging Pattern
[fill from Step 5 — actual log call examples]

## Caching Pattern
[fill from Step 5 — actual cache usage]

## Error Handling Pattern
[fill from Step 5 — exception classes + global handler]

## Authentication Pattern
[fill from Step 5 — actual auth setup]

## Migration Commands
[fill from Step 5 — actual commands used]
```

---

### FILE 2: `frontend-stack.skill.md`

```skill
---
name: Frontend Stack
description: Technology-specific stack details for the frontend. Consult this skill for framework version, environment configuration, naming conventions, template syntax, and styling approach.
---

# Frontend Stack — [detected framework + version]

## Technology Stack
[fill from Step 1]

## Folder Structure
[fill from Step 2 — actual paths]

## Naming Conventions
[fill from Step 3 — real examples]

## Environment Configuration
[fill from Step 5 — actual environment file content]

## Module / Bootstrap Setup
[fill from Step 5 — actual entry point / app module]

## Template Syntax
[fill from Step 7 — actual template patterns]

## CSS / Styling Approach
[fill from Step 7 — actual CSS/SCSS/Tailwind approach]
```

---

### FILE 3: `backend-patterns.skill.md`

```skill
---
name: Backend Code Patterns
description: Reference code templates for backend implementation. Consult this skill when writing controllers, services, entities, repositories, DTOs, or DI registration.
---

# Backend Code Patterns — [framework name]

[Write a section for each of the 7 items from Step 6.
Use real code extracted from the codebase with business names replaced by Product/Order.
Add a one-line comment above each template explaining when to use it.]
```

---

### FILE 4: `frontend-patterns.skill.md`

```skill
---
name: Frontend Code Patterns
description: Reference code templates for frontend implementation. Consult this skill when writing components, services, models, routing, forms, or interceptors.
---

# Frontend Code Patterns — [framework name]

[Write a section for each of the 7 items from Step 7.
Use real code extracted from the codebase with business names replaced by Product.
Add a one-line comment above each template explaining when to use it.]
```

---

### FILE 5: `testing-patterns.skill.md`

```skill
---
name: Testing Patterns
description: Reference templates for unit tests, integration tests, and mocking. Consult this skill when writing or reviewing tests.
---

# Testing Patterns — [test framework names]

[Write a section for each of the 4 items from Step 8.
Use real code structure with business names replaced by Product.
Show setup, arrange, act, assert clearly.]
```

---

### FILE 6: `api-design.skill.md`

```skill
---
name: API Design Patterns
description: Reference for REST API conventions, status codes, pagination, DTOs, and error responses used in this project.
---

# API Design Patterns

## Route Patterns
[extract from actual controllers/endpoints in the codebase]

## HTTP Methods and Status Codes
[extract what status codes are actually returned, with examples]

## Pagination
[extract actual pagination implementation if present]

## DTO Naming Convention
[extract from actual DTO files]

## Error Response Shape
[extract from actual error handler / response examples]
```

---

## VALIDATION

After generating all 6 files, verify:
- All code templates are extracted from REAL files, not invented
- No placeholder text remains (no "[fill from...]" left unfilled)
- Each skill file has at least 50 lines of content
- Business-specific names have been replaced with generic Product/Order examples

## IMPORTANT
Do NOT generate or modify:
- agents/ files
- instructions/ files
- Any file outside the 6 skill files listed above

These files are generic and must never be modified during reverse engineering.
```

---

## After generating the skills

1. Place the 6 generated files into `skills/` in your project
2. The Squad will automatically use them as on-demand references
3. No changes needed to `agents/` or `instructions/`

---

## File structure after setup

```
your-project/
  agents/                          ← copy from copilot-dev-team (GENERIC, never modify)
  instructions/                    ← copy from copilot-dev-team (GENERIC, never modify)
  skills/
    backend-stack.skill.md         ← GENERATED for your project
    frontend-stack.skill.md        ← GENERATED for your project
    backend-patterns.skill.md      ← GENERATED for your project
    frontend-patterns.skill.md     ← GENERATED for your project
    testing-patterns.skill.md      ← GENERATED for your project
    api-design.skill.md            ← GENERATED for your project
```

## To regenerate skills after a major change

Re-run the prompt above on the updated codebase and replace the skill files.
Never regenerate `agents/` or `instructions/` — those never change.
