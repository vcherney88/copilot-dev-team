```skill
---
name: Testing Patterns
description: Reference templates for unit tests, integration tests, and mocking. Consult this skill when writing or reviewing tests.
---

# Testing Patterns

> Adapt to the testing stack defined in the project. These templates show the expected shape.

## Backend Unit Tests (xUnit + Moq)

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

    [Fact]
    public async Task GetByIdAsync_NotFound_ThrowsNotFoundException()
    {
        var repo = new Mock<IRepository<Product>>();
        repo.Setup(r => r.GetByIdAsync(999)).ReturnsAsync((Product?)null);
        var sut = new ProductService(repo.Object, Mock.Of<ILogger<ProductService>>());

        await Assert.ThrowsAsync<NotFoundException>(() => sut.GetByIdAsync(999));
    }
}
```

### Backend Test Rules
- Mock interfaces, not concrete classes or DbContext.
- `WebApplicationFactory<Program>` for integration tests.
- Test naming: `Method_Scenario_ExpectedResult`.
- One assertion per test (when practical).
- Arrange → Act → Assert structure.

## Backend Integration Tests

```csharp
public class ProductsApiTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public ProductsApiTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetProducts_ReturnsOk()
    {
        var response = await _client.GetAsync("/api/products");
        response.EnsureSuccessStatusCode();
    }
}
```

## Frontend Unit Tests (Jasmine/Karma or Jest)

```typescript
describe('ProductService', () => {
  let service: ProductService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({ imports: [HttpClientTestingModule] });
    service = TestBed.inject(ProductService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  it('should fetch all products', () => {
    service.getAll().subscribe(p => expect(p.length).toBe(2));
    httpMock.expectOne(/products/).flush([{ id: 1 }, { id: 2 }]);
  });

  afterEach(() => httpMock.verify());
});
```

### Frontend Test Rules
- Mock HttpClient via `HttpClientTestingModule`.
- Mock services in component tests via `TestBed.overrideProvider`.
- Test naming: `should [behavior] when [condition]`.
- Test observable streams with `subscribe` + `flush`.

## Component Tests

```typescript
describe('ProductListComponent', () => {
  let component: ProductListComponent;
  let fixture: ComponentFixture<ProductListComponent>;
  let mockService: jasmine.SpyObj<ProductService>;

  beforeEach(() => {
    mockService = jasmine.createSpyObj('ProductService', ['getAll']);
    mockService.getAll.and.returnValue(of([{ id: 1, name: 'Test' }]));

    TestBed.configureTestingModule({
      declarations: [ProductListComponent],
      providers: [{ provide: ProductService, useValue: mockService }]
    });

    fixture = TestBed.createComponent(ProductListComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should display products', () => {
    const items = fixture.nativeElement.querySelectorAll('.product-item');
    expect(items.length).toBe(1);
  });
});
```

## TDD Workflow Reminder

1. **Red** — Write a failing test that describes the expected behavior
2. **Green** — Write the minimum code to make it pass
3. **Refactor** — Clean up without changing behavior, re-run tests

```
