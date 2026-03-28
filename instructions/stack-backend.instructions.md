---
applyTo: "**/*.cs"
---

# Backend Conventions — .NET Core 10

## API Layer

### Controllers
```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IProductService _productService;

    public ProductsController(IProductService productService)
    {
        _productService = productService;
    }

    [HttpGet("{id:int}")]
    [ProducesResponseType(typeof(ProductDto), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> GetById(int id) { ... }

    [HttpPost]
    [ProducesResponseType(typeof(ProductDto), StatusCodes.Status201Created)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> Create([FromBody] CreateProductRequest request) { ... }
}
```
- Inject service via constructor
- Use `[ProducesResponseType]` on every endpoint for Swagger
- Return `IActionResult` or `ActionResult<T>`
- Controllers do NOT contain business logic

### DTOs
- Separate classes per use case: `ProductDto` (response), `CreateProductRequest`, `UpdateProductRequest`
- Never expose EF Core entities in responses
- Request DTOs carry validation attributes (DataAnnotations) or are validated via FluentValidation

### Validation
```csharp
// DataAnnotations on request DTO
public class CreateProductRequest
{
    [Required]
    [MaxLength(200)]
    public string Name { get; set; } = string.Empty;

    [Range(0.01, double.MaxValue)]
    public decimal Price { get; set; }
}
```
```csharp
// Or FluentValidation (if used in project)
public class CreateProductRequestValidator : AbstractValidator<CreateProductRequest>
{
    public CreateProductRequestValidator()
    {
        RuleFor(x => x.Name).NotEmpty().MaximumLength(200);
        RuleFor(x => x.Price).GreaterThan(0);
    }
}
```

### Swagger
- Enable XML comments in .csproj: `<GenerateDocumentationFile>true</GenerateDocumentationFile>`
- Add `/// <summary>` on public controller methods
- Use `[ProducesResponseType]` on every endpoint

### Authentication / Authorization
- Use `[Authorize]` at controller or action level
- Use `[AllowAnonymous]` to override on public endpoints
- Role-based: `[Authorize(Roles = "Admin")]`

## Business Layer

### Services
```csharp
public interface IProductService
{
    Task<ProductDto> GetByIdAsync(int id);
    Task<IEnumerable<ProductDto>> GetAllAsync();
    Task<ProductDto> CreateAsync(CreateProductRequest request);
    Task UpdateAsync(int id, UpdateProductRequest request);
    Task DeleteAsync(int id);
}

public class ProductService : IProductService
{
    private readonly IRepository<Product> _repository; // generic repo for CRUD
    private readonly ILogger<ProductService> _logger;

    public ProductService(IRepository<Product> repository, ILogger<ProductService> logger)
    {
        _repository = repository;
        _logger = logger;
    }
}
```
- All business rules live in the service
- Throw typed domain exceptions (e.g., `NotFoundException`, `BusinessException`)
- Let global exception middleware map exceptions to HTTP status codes
- Log at service boundaries with structured context (`_logger.LogInformation("Getting product {ProductId}", id)`)

## Data Layer

### Entities
```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public decimal Price { get; set; }
    public int CategoryId { get; set; }
    public Category Category { get; set; } = null!;
    public DateTime CreatedAt { get; set; }
    public DateTime UpdatedAt { get; set; }
}
```
- Plain C# classes — NO data annotations for DB config
- Navigation properties for relationships

### Entity Configuration (Fluent API)
```csharp
public class ProductConfiguration : IEntityTypeConfiguration<Product>
{
    public void Configure(EntityTypeBuilder<Product> builder)
    {
        builder.ToTable("products");
        builder.HasKey(x => x.Id);
        builder.Property(x => x.Name).HasColumnName("name").HasMaxLength(200).IsRequired();
        builder.Property(x => x.Price).HasColumnName("price").HasColumnType("decimal(18,2)");
        builder.Property(x => x.CategoryId).HasColumnName("category_id");
        builder.HasIndex(x => x.CategoryId);
        builder.HasOne(x => x.Category).WithMany().HasForeignKey(x => x.CategoryId);
    }
}
```
- All DB configuration via Fluent API in `IEntityTypeConfiguration<T>` classes
- Table names: snake_case plural
- Column names: snake_case
- Always define indexes on foreign keys

### DbContext
```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

    public DbSet<Product> Products => Set<Product>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.ApplyConfigurationsFromAssembly(typeof(AppDbContext).Assembly);
    }
}
```

### Generic Repository
```csharp
public interface IRepository<T> where T : class
{
    Task<T?> GetByIdAsync(int id);
    Task<IEnumerable<T>> GetAllAsync();
    Task AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(int id);
}
```

### Specific Repository (only when custom queries needed)
```csharp
public interface IProductRepository : IRepository<Product>
{
    Task<IEnumerable<Product>> GetByCategoryAsync(int categoryId);
}
```

### Migrations
```bash
dotnet ef migrations add AddProductTable
dotnet ef database update
```
- Never modify the database manually
- One migration per logical change

## Dependency Injection (Program.cs)
```csharp
// DbContext
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseNpgsql(builder.Configuration.GetConnectionString("DefaultConnection")));

// Generic repository (registered once, used by all entities)
builder.Services.AddScoped(typeof(IRepository<>), typeof(Repository<>));

// Specific repositories (only if they exist)
builder.Services.AddScoped<IProductRepository, ProductRepository>();

// Services
builder.Services.AddScoped<IProductService, ProductService>();
```

## Testing

### Unit Tests (xUnit + Moq)
```csharp
public class ProductServiceTests
{
    [Fact]
    public async Task GetByIdAsync_ExistingProduct_ReturnsProductDto()
    {
        var mockRepo = new Mock<IRepository<Product>>();
        mockRepo.Setup(r => r.GetByIdAsync(1)).ReturnsAsync(new Product { Id = 1, Name = "Test" });
        var service = new ProductService(mockRepo.Object, Mock.Of<ILogger<ProductService>>());

        var result = await service.GetByIdAsync(1);

        Assert.NotNull(result);
        Assert.Equal("Test", result.Name);
    }
}
```
- Test naming: `MethodName_Scenario_ExpectedResult`
- Mock repository interfaces, never DbContext directly
- Use `WebApplicationFactory<Program>` for integration tests
