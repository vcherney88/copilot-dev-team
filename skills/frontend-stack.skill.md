```skill
---
name: Frontend Stack
description: Technology-specific stack details for the frontend. Consult this skill for framework version, environment configuration, naming conventions, folder structure, and framework-specific setup.
---

# Frontend Stack — Angular

> This skill contains ALL technology-specific frontend details for this project.
> To migrate to a different frontend stack (e.g., React, Vue, Flutter), replace this skill file.

## Technology Stack

| Component | Technology | Version |
|---|---|---|
| Frontend framework | Angular | latest LTS |
| Language | TypeScript | latest compatible |
| Styling | SCSS | - |
| HTTP | Angular HttpClient | built-in |
| Forms | Angular Reactive Forms | built-in |
| State | Component + Service state (no NgRx unless needed) | - |
| Testing | Jasmine + Karma (unit), Playwright (e2e) | - |
| Build | Angular CLI | latest |

## Folder Structure

```
src/app/
  core/
    services/        ← singleton services, auth, interceptors
    interceptors/
    guards/
  shared/
    components/      ← reusable dumb components
    pipes/
    directives/
  features/
    {feature}/
      components/    ← smart + dumb components for the feature
      services/      ← feature-specific services
      models/        ← TypeScript interfaces/models
      {feature}.module.ts
      {feature}-routing.module.ts
```

## Naming Conventions

| Scope | Convention | Example |
|---|---|---|
| TS variables / methods | camelCase | `getProducts()`, `isLoading` |
| TS classes / interfaces | PascalCase | `ProductService`, `ProductDto` |
| Angular component files | kebab-case + suffix | `product-list.component.ts` |
| Angular service files | kebab-case + suffix | `product.service.ts` |
| Angular model files | kebab-case + suffix | `product.model.ts` |
| Angular module files | kebab-case + suffix | `product.module.ts` |
| CSS class names | kebab-case | `.product-card`, `.btn-primary` |
| Test files | same name + `.spec.ts` | `product.service.spec.ts` |
| Test methods | `should ... when ...` | `should return products when API succeeds` |

## Environment Configuration

```typescript
// src/environments/environment.ts
export const environment = {
  production: false,
  apiBaseUrl: 'http://localhost:5000',
};

// src/environments/environment.prod.ts
export const environment = {
  production: true,
  apiBaseUrl: 'https://api.yourapp.com',
};
```

- **Never hardcode URLs** in services — always use `environment.apiBaseUrl`
- Add new environment variables here, not scattered through the codebase

## Module and App Setup

```typescript
// AppModule (or standalone bootstrap in Angular 17+)
@NgModule({
  imports: [
    BrowserModule,
    HttpClientModule,
    AppRoutingModule,
  ],
  providers: [
    { provide: HTTP_INTERCEPTORS, useClass: ErrorInterceptor, multi: true },
    { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true },
  ],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

## Template Syntax (Angular 17+ preferred)

```html
<!-- Angular 17+: control flow syntax -->
@if (product) {
  <div>{{ product.name }}</div>
} @else {
  <p>No product found</p>
}

@for (item of items; track item.id) {
  <app-product-card [product]="item" />
}

<!-- Angular <17: structural directives (use if project is on older version) -->
<div *ngIf="product">{{ product.name }}</div>
<div *ngFor="let item of items; trackBy: trackById">...</div>
```

- Always `trackBy` / `track` in loops — prevents unnecessary DOM re-renders
- `async` pipe over manual `.subscribe()` in templates
- Check project Angular version and use the appropriate syntax

## CSS/SCSS Conventions

```scss
// Component styles: scoped by default (ViewEncapsulation.Emulated)
// Use BEM-like naming for clarity
.product-card {
  &__title { font-size: 1.2rem; }
  &__price { color: $primary; }
  &--highlighted { border: 2px solid $accent; }
}

// Global styles in styles.scss only
// Feature-specific styles in component .scss files
```

```
