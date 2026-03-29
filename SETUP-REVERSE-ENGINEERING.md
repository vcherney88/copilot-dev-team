# Reverse Engineering Prompt — Setup Instructions for Existing Projects

> **Purpose**: Analyze an existing codebase to generate the 4 `instructions/*.instructions.md` files needed by the Squad.

Use this prompt when integrating the Squad into a project that already has code, architecture, and conventions defined. The goal is to extract those conventions and generate the instruction files automatically.

---

## Prompt to Use

Copy and paste this prompt to an AI agent (or use it yourself for manual analysis):

```
You are a Senior Software Architect performing reverse engineering on an existing codebase to generate coding instructions for an AI coding squad.

# Goal
Analyze the codebase and produce 4 instruction files:
1. `instructions/architecture.instructions.md` — stack, layers, folder structure, naming, cross-cutting
2. `instructions/backend.instructions.md` — backend code patterns and templates
3. `instructions/frontend.instructions.md` — frontend code patterns and templates
4. `instructions/standards.instructions.md` — generic (copy as-is from the Squad template, no changes needed)

# Analysis Steps

## 1. Stack Detection

Search the codebase for:
- **Backend framework**: Check `*.csproj`, `package.json`, `pom.xml`, `requirements.txt`, `Gemfile`, `go.mod`
  - Look for: .NET (Core/Framework version), Node.js (Express, Nest), Java (Spring Boot), Python (Django, FastAPI), Ruby (Rails), Go, PHP (Laravel)
- **Frontend framework**: Check `package.json`, `angular.json`, `vite.config`, `next.config`, `pubspec.yaml`
  - Look for: Angular, React, Vue, Svelte, Blazor, Razor Pages, Flutter
- **Database**: Check connection strings in `appsettings.json`, `.env`, config files
  - Look for: PostgreSQL, SQL Server, MySQL, MongoDB, SQLite, Oracle
- **ORM/Data access**: Check dependencies
  - Look for: Entity Framework (Core), Dapper, Hibernate, Sequelize, TypeORM, Prisma, Django ORM

**Output**: Fill the "Stack" section of `architecture.instructions.md`.

---

## 2. Layer & Folder Structure

Analyze the folder hierarchy:
- How many layers? (2-tier, 3-tier, N-tier, Clean Architecture, Hexagonal, etc.)
- What are the layer names? (Api/Controllers, Business/Services/Application, Data/Infrastructure/Persistence, Domain)
- Where do these live? (separate projects, folders within one project, modules)

Look for:
- Controller/Endpoint files → API Layer
- Service/UseCase files → Business Layer
- Repository/DbContext files → Data Layer
- Entity/Model files → Domain or Data

**Output**: Fill "Layer Structure" and "Folder Structure" sections of `architecture.instructions.md`.

---

## 3. Dependency Flow & Patterns

Identify architectural patterns:
- **Repository Pattern**: Search for `IRepository`, `Repository<T>`, `GenericRepository`
  - Is there a generic repository? What methods does it have?
  - Are there specific repositories for complex queries?
- **Unit of Work**: Search for `IUnitOfWork`, `UnitOfWork`
- **Service Pattern**: How are services structured? Interface + implementation? Naming convention?
- **DTOs**: Separate request/response objects? Naming suffix (Dto, Request, Response, ViewModel)?
- **Dependency Injection**: Where is DI configured? (Program.cs, Startup.cs, main.ts, AppModule)

**Output**: Fill "Repository Pattern", "Dependency Flow" sections of `architecture.instructions.md`.

---

## 4. Naming Conventions

Sample 10-20 files across layers and extract naming patterns:

### Backend (if .NET/C#/Java)
- Public members: PascalCase or camelCase?
- Private fields: `_camelCase`, `camelCase`, `m_camelCase`?
- Interfaces: `I` prefix (IProductService) or not?
- Async methods: `Async` suffix or not?
- Test methods: `MethodName_Scenario_Result` or `should...when...` or `Test_MethodName`?

### Frontend (if TypeScript/JavaScript)
- Variables/methods: camelCase or snake_case?
- Classes/interfaces: PascalCase?
- File names: kebab-case, camelCase, PascalCase?
- Component naming: `.component.ts`, `.tsx`, `.vue`?

### Database
- Table names: snake_case, PascalCase, plural or singular?
- Column names: snake_case, camelCase, PascalCase?
- Foreign keys: `CategoryId`, `category_id`, `categoryID`?

### DTOs/Models
- Response models: `ProductDto`, `ProductResponse`, `ProductViewModel`?
- Request models: `CreateProductRequest`, `ProductCreateDto`, `CreateProduct`?

**Output**: Fill "Naming Conventions" table in `architecture.instructions.md`.

---

## 5. API Conventions

If the project has a REST API:
- Route patterns: `/api/products`, `/products`, `/v1/products`?
- HTTP methods: RESTful (GET/POST/PUT/DELETE) or custom?
- Status codes: What's returned for success/error? (200, 201, 204, 400, 404, 409, 500)
- Pagination: Query params (`?page=1&pageSize=20`), headers, or custom?
- Versioning: URL versioning (`/v1/`), header (`api-version`), or none?

**Output**: Fill "API Conventions" section in `architecture.instructions.md`.

---

## 6. Cross-Cutting Concerns

### Logging
- **Library**: Search imports/dependencies for Serilog, NLog, log4net, Winston, Bunyan, Logback
- **Sink**: Files, database, console, cloud (Application Insights, CloudWatch)?
- **Format**: Structured logging with templates `{PropertyName}` or string interpolation?
- Look at existing log calls to extract the pattern

### Caching
- **Strategy**: In-memory (`IMemoryCache`, `MemoryCache`), Redis, Memcached?
- **Where used**: Check service methods for caching logic
- **TTL**: Are there expiration policies defined?

### Error Handling
- **Global exception handling**: Middleware in `Program.cs`/`Startup.cs`, filter in `@ControllerAdvice`, error boundary in React?
- **Custom exceptions**: Search for `*Exception.cs`, `*Error.ts` files
  - What base class? (`Exception`, `ApplicationException`, custom base?)
  - What exceptions exist? (`NotFoundException`, `ValidationException`, `BusinessException`)
- **HTTP mapping**: How are exceptions mapped to status codes?

### Authentication & Authorization
- **Strategy**: JWT Bearer, Cookie, OAuth, Identity, Auth0, Keycloak?
- **Attributes/Decorators**: `[Authorize]`, `@Secured`, `@UseGuards`, middleware?
- **Roles or Policies**: Role-based (`[Authorize(Roles = "Admin")]`) or policy-based?

### Data Seeding
- **Migrations**: EF Core migrations, Flyway, Liquibase, Alembic, Django migrations?
- **Seed Data**: SQL scripts, code-based seeding (`HasData`), separate seeder classes?

**Output**: Fill "Cross-Cutting Concerns" section in `architecture.instructions.md`.

---

## 7. Backend Code Patterns (for `backend.instructions.md`)

Find 2-3 representative examples of each:

### Controller
- Locate a typical controller (e.g., ProductsController, UsersController)
- Extract the pattern:
  - Base class (ControllerBase, Controller, ApiController)?
  - Route attribute pattern?
  - DI injection (constructor, property)?
  - Return types (IActionResult, ActionResult<T>, Task<IActionResult>)?
  - Attributes (`[HttpGet]`, `[ProducesResponseType]`, `[Authorize]`)?

### Service
- Locate a service interface and implementation
- Extract:
  - Naming convention (IProductService / ProductService)?
  - Method signatures (async/await, return types)?
  - Dependencies injected?
  - Logging pattern?

### Entity
- Locate an entity/model class
- Extract:
  - Plain POCO or attributes (`[Key]`, `[Required]`)?
  - Navigation properties?
  - Constructor pattern (parameterless, with params)?

### Entity Configuration (if EF Core)
- Locate `IEntityTypeConfiguration<T>` classes
- Extract:
  - Table/column naming (`.ToTable("products")`, `.HasColumnName("created_at")`)?
  - Indexes, relationships, constraints?

### Repository (if exists)
- Generic repository interface/implementation
- Specific repository example

### DI Registration
- Where is DI configured? Extract the pattern for registering DbContext, repositories, services

### Testing
- Locate unit test files (xUnit, NUnit, MSTest, Jest, Mocha)
- Extract:
  - Test class naming (`*Tests`, `*Test`, `*.spec.ts`)?
  - Test method naming?
  - Mocking library (Moq, NSubstitute, Sinon, Jest)?
  - Setup/teardown pattern?

**Output**: Create `backend.instructions.md` with concise code templates extracted from the codebase.

---

## 8. Frontend Code Patterns (for `frontend.instructions.md`)

Find 2-3 representative examples of each:

### Component
- Smart/container component example
- Dumb/presentational component example
- Extract: file naming, folder structure, lifecycle hooks, state management

### Service
- HTTP service example (calling backend API)
- Extract: HttpClient/fetch/axios usage, Observable/Promise, error handling, base URL handling

### Model/Interface
- DTO/model interface
- Extract: naming (`.model.ts`, `.interface.ts`, `.types.ts`)?

### Routing
- Routing configuration
- Extract: lazy loading, route guards, param handling

### Forms
- Reactive form example or template-driven form
- Extract: validation pattern, error display

### State Management (if applicable)
- Redux/NgRx/Vuex/Context API/Riverpod pattern

### Testing
- Component test example
- Service test example
- Extract: mocking pattern, test utilities (TestBed, render, etc.)

**Output**: Create `frontend.instructions.md` with concise code templates extracted from the codebase.

---

## 9. Generate the Files

Using the extracted information, generate the 4 instruction files:

### `architecture.instructions.md`
```markdown
## Stack
[fill from step 1]

## Layer Structure
[fill from step 2]

## Folder Structure
[fill from step 2]

## Repository Pattern
[fill from step 3]

## Dependency Flow
[fill from step 3]

## API Conventions
[fill from step 5]

## Naming Conventions
[fill from step 4 — use table format]

## Cross-Cutting Concerns
[fill from step 6]
```

### `backend.instructions.md`
Use the template structure, replace examples with real code from the codebase (step 7).

### `frontend.instructions.md`
Use the template structure, replace examples with real code from the codebase (step 8).

### `standards.instructions.md`
Copy as-is from the Squad template — this file is generic and doesn't change per project.

---

## Validation Checklist

After generating the files, verify:
- [ ] All 4 files created
- [ ] Stack technologies correctly identified
- [ ] Folder paths match actual project structure
- [ ] Naming conventions extracted from at least 10 real examples
- [ ] Code templates are real snippets from the codebase (not invented)
- [ ] Cross-cutting concerns (logging, caching, auth) patterns identified
- [ ] No hardcoded assumptions — everything derived from the codebase
```

---

## How to Use This Prompt

### Option 1: AI Agent (Recommended)
1. Copy the entire prompt above
2. Paste it to an AI agent with codebase access (e.g., Copilot Chat, Claude with MCP, Cursor)
3. Point it to the project root folder
4. Review and validate the generated files

### Option 2: Manual Analysis
1. Follow the analysis steps manually
2. Use find/grep to search for patterns
3. Sample multiple files to confirm conventions
4. Fill the instruction files yourself

### Option 3: Hybrid
1. Use AI to extract raw data (file lists, code samples)
2. Manually review and refine the patterns
3. Generate the final instruction files

---

## After Setup

Once the 4 instruction files are created:
1. Place them in `instructions/` folder at the project root
2. Copy the `agents/` folder into the project
3. Copy `squad-overview.md` to the project root
4. The Squad is ready to use
5. Test with a small change request to validate the instructions are accurate
