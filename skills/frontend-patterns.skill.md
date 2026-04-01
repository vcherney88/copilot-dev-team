```skill
---
name: Frontend Code Patterns
description: Reference code templates for frontend implementation. Consult this skill when writing components, services, models, routing, forms, or interceptors.
---

# Frontend Code Patterns

> These are reference templates extracted from the project. Follow this shape for consistency.
> Adapt to the frontend framework defined in `architecture.instructions.md`.

## Models (mirror backend DTOs)

```typescript
export interface Product {
  id: number;
  name: string;
  price: number;
  categoryId: number;
  createdAt: string;
}

export interface CreateProductRequest {
  name: string;
  price: number;
  categoryId: number;
}
```

## Service (HttpClient, typed, Observable)

```typescript
@Injectable({ providedIn: 'root' })
export class ProductService {
  private readonly api = `${environment.apiBaseUrl}/api/products`;

  constructor(private http: HttpClient) {}

  getAll(page = 1, pageSize = 20): Observable<Product[]> {
    return this.http.get<Product[]>(this.api, {
      params: { page, pageSize }
    });
  }

  getById(id: number): Observable<Product> {
    return this.http.get<Product>(`${this.api}/${id}`);
  }

  create(req: CreateProductRequest): Observable<Product> {
    return this.http.post<Product>(this.api, req);
  }

  update(id: number, req: UpdateProductRequest): Observable<void> {
    return this.http.put<void>(`${this.api}/${id}`, req);
  }

  delete(id: number): Observable<void> {
    return this.http.delete<void>(`${this.api}/${id}`);
  }
}
```
- API URL from `environment.ts` — never hardcode.
- Return `Observable<T>` — never subscribe inside services.
- Typed generics: `http.get<Product[]>(...)`.

## Components

**Smart (container)** — fetches data, connects to services:
```typescript
@Component({ selector: 'app-product-list', templateUrl: './product-list.component.html' })
export class ProductListComponent implements OnInit {
  products$ = EMPTY as Observable<Product[]>;

  constructor(private productService: ProductService) {}
  ngOnInit() { this.products$ = this.productService.getAll(); }
}
```

**Dumb (presentational)** — `@Input()` / `@Output()` only, no service injection:
```typescript
@Component({ selector: 'app-product-card', templateUrl: './product-card.component.html' })
export class ProductCardComponent {
  @Input() product!: Product;
  @Output() deleted = new EventEmitter<number>();
}
```

## Templates

- `async` pipe over manual subscriptions: `*ngFor="let p of products$ | async"`
- `trackBy` on every `*ngFor`
- Check Angular version: `@if`/`@for` (17+) vs `*ngIf`/`*ngFor`
- Semantic HTML: `<article>`, `<section>`, `<nav>`, `<main>`
- Accessibility: `aria-label`, `<label for="">`, keyboard navigable

## Reactive Forms

```typescript
this.form = this.fb.group({
  name: ['', [Validators.required, Validators.maxLength(200)]],
  price: [null, [Validators.required, Validators.min(0.01)]],
});
```
- Validation errors inline, next to field.
- Disable submit when form invalid.
- Feedback after submission (success/error toast or message).

## Routing

```typescript
const routes: Routes = [
  { path: '', component: ProductListComponent },
  { path: 'new', component: ProductFormComponent },
  { path: ':id', component: ProductDetailComponent },
  { path: ':id/edit', component: ProductFormComponent },
];
```
- Lazy load features: `loadChildren: () => import(...)`.
- Route guards (`CanActivate`) for protected routes.

## Environment

```typescript
export const environment = {
  production: false,
  apiBaseUrl: 'http://localhost:5000',
};
```

## Error Interceptor

```typescript
@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<unknown>, next: HttpHandler) {
    return next.handle(req).pipe(
      catchError((err: HttpErrorResponse) => {
        // 401 → redirect login, 403 → forbidden, 500 → generic message
        return throwError(() => err);
      })
    );
  }
}
```

```
