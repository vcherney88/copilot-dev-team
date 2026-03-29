```instructions
---
applyTo: "**/*.cs"
---

# Backend Patterns — .NET

> Reference templates. Follow this shape for consistency. Don't duplicate rules from `architecture.instructions.md`.

## Controller (thin — validate, call service, return response)

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController(IProductService productService) : ControllerBase
{
    [HttpGet("{id:int}")]
    [ProducesResponseType(typeof(ProductDto), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> GetById(int id)
        => Ok(await productService.GetByIdAsync(id));

    [HttpPost]
    [ProducesResponseType(typeof(ProductDto), StatusCodes.Status201Created)]
    public async Task<IActionResult> Create([FromBody] CreateProductRequest request)
    {
        var result = await productService.CreateAsync(request);
        return CreatedAtAction(nameof(GetById), new { id = result.Id }, result);
    }
}
```
- `[ProducesResponseType]` on every endpoint (Swagger).
- XML comments on public methods: `/// <summary>`.
- Primary constructor for DI injection.

## Validation (on request DTOs)

```csharp
public class CreateProductRequest
{
    [Required, MaxLength(200)]
    public string Name { get; set; } = string.Empty;

    [Range(0.01, double.MaxValue)]
    public decimal Price { get; set; }
}
```
Or FluentValidation if adopted: `AbstractValidator<T>` with `RuleFor(...)`.

## Service (all business logic here)

```csharp
public interface IProductService
{
    Task<ProductDto> GetByIdAsync(int id);
    Task<IEnumerable<ProductDto>> GetAllAsync();
    Task<ProductDto> CreateAsync(CreateProductRequest request);
    Task UpdateAsync(int id, UpdateProductRequest request);
    Task DeleteAsync(int id);
}

public class ProductService(IRepository<Product> repository, ILogger<ProductService> logger) : IProductService
{
    // business rules, mapping, logging at boundaries
    // throw NotFoundException, BusinessException for error cases
}
```

## Entity (plain C# class, no data annotations)

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

## Entity Configuration (Fluent API only)

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
- All DB config via Fluent API in `IEntityTypeConfiguration<T>`. No annotations on entities.
- Table/column names: snake_case (see naming conventions in `architecture.instructions.md`).
- Always index foreign keys.

## DbContext

```csharp
public class AppDbContext(DbContextOptions<AppDbContext> options) : DbContext(options)
{
    public DbSet<Product> Products => Set<Product>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
        => modelBuilder.ApplyConfigurationsFromAssembly(typeof(AppDbContext).Assembly);
}
```

## DI Registration (Program.cs)

```csharp
builder.Services.AddDbContext<AppDbContext>(o => o.UseNpgsql(connectionString));
builder.Services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
// Specific repos only if they exist:
builder.Services.AddScoped<IProductRepository, ProductRepository>();
// Services:
builder.Services.AddScoped<IProductService, ProductService>();
```

## Migrations

```bash
dotnet ef migrations add MigrationName
dotnet ef database update
```
One migration per logical change. Never modify the database manually.

## Unit Test (xUnit + Moq)

```csharp
public class ProductServiceTests
{
    [Fact]
    public async Task GetByIdAsync_ExistingProduct_ReturnsDto()
    {
        var repo = new Mock<IRepository<Product>>();
        repo.Setup(r => r.GetByIdAsync(1)).ReturnsAsync(new Product { Id = 1, Name = "Test" });
        var sut = new ProductService(repo.Object, Mock.Of<ILogger<ProductService>>());

        var result = await sut.GetByIdAsync(1);

        Assert.Equal("Test", result.Name);
    }
}
```
- Mock interfaces, not DbContext.
- `WebApplicationFactory<Program>` for integration tests.

```
