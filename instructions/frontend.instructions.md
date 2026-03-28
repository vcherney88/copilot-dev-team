---
applyTo: "**/*.ts,**/*.html,**/*.scss"
---

# Frontend Conventions — Angular

## Module & Feature Structure

```
src/app/
  core/
    services/          ← singleton app-wide services
    interceptors/      ← HttpInterceptor for auth headers, error handling
    guards/            ← route guards
  shared/
    components/        ← reusable presentational components
    pipes/
    directives/
  features/
    product/           ← one folder per feature
      components/
        product-list/
          product-list.component.ts
          product-list.component.html
          product-list.component.scss
          product-list.component.spec.ts
      services/
        product.service.ts
        product.service.spec.ts
      models/
        product.model.ts
      product-routing.module.ts
      product.module.ts
```

## Models (TypeScript Interfaces)

Mirror backend DTOs exactly — keep in `models/` folder inside the feature:

```typescript
// models/product.model.ts
export interface Product {
  id: number;
  name: string;
  description: string;
  price: number;
  categoryId: number;
  createdAt: string;
}

export interface CreateProductRequest {
  name: string;
  description: string;
  price: number;
  categoryId: number;
}

export interface UpdateProductRequest {
  name: string;
  description: string;
  price: number;
}
```

## Services

```typescript
// services/product.service.ts
import { Injectable } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { Observable } from 'rxjs';
import { environment } from '../../../environments/environment';
import { Product, CreateProductRequest, UpdateProductRequest } from '../models/product.model';

@Injectable({ providedIn: 'root' })
export class ProductService {
  private readonly apiUrl = `${environment.apiBaseUrl}/api/products`;

  constructor(private http: HttpClient) {}

  getAll(page = 1, pageSize = 20): Observable<Product[]> {
    const params = new HttpParams().set('page', page).set('pageSize', pageSize);
    return this.http.get<Product[]>(this.apiUrl, { params });
  }

  getById(id: number): Observable<Product> {
    return this.http.get<Product>(`${this.apiUrl}/${id}`);
  }

  create(request: CreateProductRequest): Observable<Product> {
    return this.http.post<Product>(this.apiUrl, request);
  }

  update(id: number, request: UpdateProductRequest): Observable<void> {
    return this.http.put<void>(`${this.apiUrl}/${id}`, request);
  }

  delete(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```
- API base URL always from `environment.ts` — never hardcode
- Typed responses: `http.get<Product[]>(...)`
- Return `Observable<T>` — do NOT subscribe inside service methods

## Components

### Smart (Container) Component
Handles data fetching and state — connects to services:

```typescript
@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
})
export class ProductListComponent implements OnInit {
  products$: Observable<Product[]> = EMPTY;

  constructor(private productService: ProductService) {}

  ngOnInit(): void {
    this.products$ = this.productService.getAll();
  }
}
```

### Presentational (Dumb) Component
Receives data via `@Input()`, emits via `@Output()` — no direct service injection:

```typescript
@Component({
  selector: 'app-product-card',
  templateUrl: './product-card.component.html',
})
export class ProductCardComponent {
  @Input() product!: Product;
  @Output() deleted = new EventEmitter<number>();

  onDelete(): void {
    this.deleted.emit(this.product.id);
  }
}
```

## Templates

- Prefer `async` pipe over manual subscriptions in components:
  ```html
  <div *ngFor="let product of products$ | async; trackBy: trackById">
  ```
- Use `trackBy` for all `*ngFor` loops
- Check Angular version in project before using `@if`/`@for` (Angular 17+) vs `*ngIf`/`*ngFor`
- Semantic HTML: use `<article>`, `<section>`, `<nav>`, `<main>` appropriately
- Accessibility: `aria-label`, `aria-describedby`, proper `<label for="">` on inputs

## Forms

### Reactive Forms (complex forms)
```typescript
this.form = this.fb.group({
  name: ['', [Validators.required, Validators.maxLength(200)]],
  price: [null, [Validators.required, Validators.min(0.01)]],
});
```

### Template in Reactive Forms
```html
<form [formGroup]="form" (ngSubmit)="onSubmit()">
  <input formControlName="name" />
  <div *ngIf="form.get('name')?.invalid && form.get('name')?.touched">
    Name is required
  </div>
  <button type="submit" [disabled]="form.invalid">Save</button>
</form>
```
- Always show validation errors inline, next to the field
- Disable submit when form is invalid
- Provide visual feedback (success/error) after submission

## Error Handling

### HttpInterceptor (global)
```typescript
@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        // log or show notification
        return throwError(() => error);
      })
    );
  }
}
```
- Register in `CoreModule` providers
- Handle 401 (redirect to login), 403 (forbidden), 500 (generic error message)

## Routing

```typescript
// product-routing.module.ts
const routes: Routes = [
  { path: '', component: ProductListComponent },
  { path: 'new', component: ProductFormComponent },
  { path: ':id', component: ProductDetailComponent },
  { path: ':id/edit', component: ProductFormComponent },
];
```
- Lazy load feature modules:
  ```typescript
  { path: 'products', loadChildren: () => import('./features/product/product.module').then(m => m.ProductModule) }
  ```
- Use route guards (`CanActivate`) for protected routes

## Environment Configuration

```typescript
// environments/environment.ts
export const environment = {
  production: false,
  apiBaseUrl: 'http://localhost:5000',
};
```
- All API URLs come from `environment.apiBaseUrl`
- Production values in `environment.prod.ts`

## Testing

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
    service.getAll().subscribe(products => expect(products.length).toBe(2));
    const req = httpMock.expectOne(`${environment.apiBaseUrl}/api/products?page=1&pageSize=20`);
    req.flush([{ id: 1 }, { id: 2 }]);
  });
});
```
- Test naming: `should [expected behavior] when [condition]`
- Mock HttpClient via `HttpClientTestingModule`
- Mock services in component tests via `TestBed.overrideProvider`
