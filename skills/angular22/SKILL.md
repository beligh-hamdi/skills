---
name: angular22
description: Angular 22 complete reference — signals, @Service, standalone components, project structure (core/features/shared), lazy routing, Transloco i18n, Signal Forms, resource APIs, change detection, template syntax, and every new v22 API. Sourced from angular.dev and this codebase's actual patterns.
sasmp_version: "1.3.0"
allowed-tools:
  - read
  - edit
  - grep
  - glob
  - exec
triggers:
  - user
  - model
---

# Angular 22 · Complete Reference

> Sources: https://angular.dev · https://blog.angular.dev/angular-v22 · this codebase

---

## 1. Angular 22 — What's New at a Glance

| Feature | Status | Impact |
|---|---|---|
| **Signal Forms** (`@angular/forms/signals`) | **Stable** | Use for all new forms |
| **`resource()` / `rxResource()` / `httpResource()`** | **Stable** | Replace manual signal+effect+fetch |
| **Angular Aria** (`@angular/aria`) | **Stable** | Headless a11y primitives |
| **`@Service()` decorator** | **Stable** | Replaces `@Injectable({ providedIn: 'root' })` |
| **`injectAsync()`** | **Stable** | Lazy DI with code-splitting |
| **OnPush as default** | **Breaking** | `ng update` adds `Eager` to existing components |
| **Fetch backend default** | **Breaking** | `withFetch()` removed; XhrBackend available separately |
| **TypeScript 6 required** | **Breaking** | TS 5.9 dropped |
| **Node 22.22+ / 24 / 26** | **Breaking** | Node 20 dropped |
| **Vitest as default** | **Breaking** | Karma removed |
| **`debounced()` signal** | Experimental | Debounce any signal, returns Resource |
| **Template spread syntax** | **Stable** | `[class]="{ ...base, active: x }"` |
| **Arrow functions in templates** | **Stable** | `(click)="() => count.set(count() + 1)"` |
| **Stacked `@switch` cases** | **Stable** | Fall-through + `@default never;` exhaustive check |
| **Platform Navigation API** | Experimental | `withExperimentalPlatformNavigation()` |
| **`injectAsync()`** | **Stable** | Lazy-load services on demand |
| **`provideBrowserGlobalErrorListeners()`** | **Stable** | Replaces manual window error handlers |

---

## 2. Project Structure

```
src/app/
├── core/               # Singleton services only — no components
│   ├── auth/           # GoogleOAuth, token interceptor, auth guard
│   ├── google-sheets/  # Sheets API layer (CRUD, tab seeding)
│   ├── i18n/           # LanguageService — lang switching, RTL, category name translation
│   ├── theme/          # ThemeService — light/dark toggle
│   ├── pwa/            # PwaService — install prompt, SW updates, offline detection
│   ├── sync/           # SyncService — periodic background data refresh
│   ├── dashboard/      # DashboardService — aggregates stats from expenses+categories
│   ├── ocr/            # OcrService — Tesseract.js receipt scanner
│   └── insights/       # InsightsService — Gemini AI prompt builder
│
├── features/           # Lazy-loaded page components (one directory per route)
│   ├── auth/           # login.component
│   ├── dashboard/      # dashboard.component (owns its chart helpers)
│   ├── expenses/       # expenses.component + expense-form/ dialog
│   ├── categories/     # categories.component + category-form/ dialog
│   ├── insights/       # insights.component
│   └── settings/       # settings.component
│
├── layout/             # Structural shell — wraps <router-outlet>
│   └── shell/          # sidenav, toolbar, language switcher, theme toggle
│
└── shared/             # Reusable across 2+ features
    ├── components/
    │   ├── chart/          # Chart.js wrapper (dumb — no inject beyond config input)
    │   └── confirm-dialog/ # Generic confirmation dialog
    ├── models/             # TypeScript interfaces + Google Sheets row mappers
    │   ├── expense.model.ts
    │   ├── category.model.ts
    │   └── app-settings.model.ts
    └── utils/
        └── crypto.util.ts  # Web Crypto API UUID wrapper
```

### Structure rules

| Layer | What goes here | What does NOT go here |
|---|---|---|
| `core/` | `@Service()` singletons, interceptors, guards | Components, dialogs |
| `features/` | Page components, their child dialogs | Shared/reusable components |
| `shared/components/` | Components used by ≥2 features | Feature-specific logic |
| `shared/models/` | Interfaces, constants, row mappers | Business logic, HTTP calls |
| `layout/` | App shell, nav, toolbar | Page content |

---

## 3. Services — `@Service()` Pattern

### 3a. Basic singleton service

```typescript
import { Service, inject, signal, computed, effect } from '@angular/core';

@Service()   // ≡ @Injectable({ providedIn: 'root' }) — always singleton
export class ThemeService {
  // 1. All injections via inject() at field level — never constructor params
  private readonly router = inject(Router);

  // 2. Private writable signal (_prefix convention)
  private readonly _scheme = signal<'light' | 'dark'>(this.loadScheme());

  // 3. Exposed as readonly — consumers cannot mutate
  readonly scheme = this._scheme.asReadonly();

  // 4. Derived state via computed()
  readonly isDark = computed(() => this._scheme() === 'dark');

  constructor() {
    // 5. Side-effects via effect() — re-runs whenever _scheme() changes
    effect(() => {
      const s = this._scheme();
      document.body.style.colorScheme = s;
      document.body.classList.toggle('dark-theme', s === 'dark');
      localStorage.setItem('theme', s);
    });
  }

  toggle(): void {
    this._scheme.update(s => s === 'light' ? 'dark' : 'light');
  }

  private loadScheme(): 'light' | 'dark' {
    return (localStorage.getItem('theme') as 'light' | 'dark') ?? 'light';
  }
}
```

### 3b. Service exposing computed state

```typescript
@Service()
export class SheetConfigService {
  private readonly auth = inject(AuthService);

  private readonly _spreadsheetId = signal<string | null>(this.loadId());

  // Public readonly — hide the writable side
  readonly spreadsheetId = this._spreadsheetId.asReadonly();

  // Boolean derived from signal — templates can call isConfigured()
  readonly isConfigured = computed(() => !!this._spreadsheetId());

  setSpreadsheetId(id: string): void {
    localStorage.setItem(this.storageKey(), id);
    this._spreadsheetId.set(id);
  }

  clearSpreadsheetId(): void {
    localStorage.removeItem(this.storageKey());
    this._spreadsheetId.set(null);
  }
}
```

### 3c. Service with RxJS interop (`toSignal`)

```typescript
import { toSignal } from '@angular/core/rxjs-interop';

@Service()
export class PwaService {
  private readonly swUpdate = inject(SwUpdate);

  readonly updateAvailable = signal(false);
  readonly isOffline       = signal(!navigator.onLine);

  // Convert Observable to Signal — must be called in injection context
  readonly versionState = toSignal(
    this.swUpdate.versionUpdates,
    { initialValue: null },
  );
}
```

### 3d. New v22 — `injectAsync()` for lazy services

```typescript
// Heavy service that should NOT be loaded at bootstrap
@Service()
export class ReportService {
  private readonly http = inject(HttpClient);
  exportPdf() { return this.http.get('/api/report', { responseType: 'blob' }); }
}

// Consumer — service is code-split, loaded only on first call
@Component({ ... })
export class AdminComponent {
  private readonly report = injectAsync(() =>
    import('@core/report/report.service').then(m => m.ReportService)
  );

  async downloadReport() {
    const svc = await this.report();   // resolves and caches
    svc.exportPdf().subscribe(saveFile);
  }
}
```

### Service rules

| Rule | Reason |
|---|---|
| `@Service()` — not `@Injectable` | Shorter, enforces `inject()` only, `providedIn: root` by default |
| `inject()` at field level | Works with `@Service()`, better IDE inference, no constructor boilerplate |
| Expose via `.asReadonly()` | Prevents external mutation; makes data flow explicit |
| `computed()` for derived values | Lazy, memoised, re-computes only when dependencies change |
| `effect()` for side-effects only | Not for deriving state — use `computed()` for that |
| No `Subject` / `BehaviorSubject` for local state | Signals replace them; use RxJS only for event streams |

---

## 4. Components — Standalone + Signals

### 4a. Full component anatomy

```typescript
import { Component, inject, signal, computed, effect, input, output, viewChild } from '@angular/core';

@Component({
  selector: 'app-categories',
  standalone: true,           // always true — no NgModules
  imports: [                  // ALL template dependencies listed here
    MatCardModule,
    MatButtonModule,
    MatIconModule,
    MatProgressSpinnerModule,
    TranslocoModule,
    RouterLink,
    CurrencyPipe,
  ],
  // Inline template + styles preferred for components < ~200 lines
  template: `
    <ng-container *transloco="let t">

      @if (loading()) {
        <mat-spinner />
      } @else if (!sheetConfig.isConfigured()) {
        <p>{{ t('categories.connect_spreadsheet_notice') }}</p>
      } @else {
        @for (cat of categories(); track cat.id) {
          <mat-card>
            <mat-icon [style.color]="cat.color">{{ cat.icon }}</mat-icon>
            <span>{{ catName(cat.name) }}</span>
          </mat-card>
        } @empty {
          <p>{{ t('categories.no_categories') }}</p>
        }
      }

    </ng-container>
  `,
  styles: [`
    :host { display: block; padding: 1rem; }
    mat-card { margin-bottom: 0.5rem; }
  `],
})
export class CategoriesComponent {
  // ── Injections ────────────────────────────────────────
  readonly sheetConfig = inject(SheetConfigService);   // public: used in template
  private readonly sheets = inject(GoogleSheetsService);
  private readonly lang   = inject(LanguageService);
  private readonly snack  = inject(MatSnackBar);

  // ── State signals ─────────────────────────────────────
  readonly loading    = signal(false);
  readonly categories = signal<Category[]>([]);

  // ── Derived / computed ────────────────────────────────
  readonly withBudget = computed(() =>
    this.categories().filter(c => c.budgetLimit > 0)
  );

  // ── Template helpers ──────────────────────────────────
  catName = (name: string) => this.lang.translateCategoryName(name);

  constructor() {
    this.load();   // called every time component is created (lazy route = each visit)
  }

  async load(): Promise<void> {
    if (!this.sheetConfig.isConfigured()) return;
    this.loading.set(true);
    try {
      this.categories.set(await this.sheets.getCategories());
    } catch (err) {
      this.snack.open(String(err), 'OK', { duration: 5000 });
    } finally {
      this.loading.set(false);
    }
  }
}
```

### 4b. `viewChild()` — replaces `@ViewChild`

```typescript
import { viewChild, effect } from '@angular/core';

export class ExpensesComponent {
  // Optional — Signal<T | undefined>; safe when element may not exist yet
  readonly sort      = viewChild(MatSort);
  readonly paginator = viewChild(MatPaginator);

  // Required — Signal<T>; throws NG0951 if element is not in DOM
  readonly canvas = viewChild.required<ElementRef<HTMLCanvasElement>>('canvas');

  constructor() {
    effect(() => {
      const sort = this.sort();
      const pag  = this.paginator();
      if (!sort || !pag) return;    // guard: only run when both are rendered
      this.dataSource.sort      = sort;
      this.dataSource.paginator = pag;
    });
  }
}
```

### 4c. `input()` and `output()` — replaces `@Input` / `@Output`

```typescript
import { Component, input, output, model, computed, numberAttribute } from '@angular/core';

@Component({ selector: 'app-stat-card', standalone: true, template: `...` })
export class StatCardComponent {
  // Required input — Signal<string>
  readonly label = input.required<string>();

  // Optional input with default
  readonly subtitle = input<string>('');

  // Input with built-in transform
  readonly value = input(0, { transform: numberAttribute });

  // Alias (public API name differs from field)
  readonly colorHex = input<string>('#000', { alias: 'color' });

  // Two-way binding via model() — parent uses [(checked)]
  readonly checked = model(false);

  // Output event
  readonly cardClick = output<string>();

  // Computed from input — reactive to parent changes
  readonly displayLabel = computed(() =>
    this.label().toUpperCase()
  );

  handleClick() {
    this.cardClick.emit(this.label());
  }
}
```

### 4d. `contentChild()` — replaces `@ContentChild`

```typescript
import { contentChild, contentChildren } from '@angular/core';

export class TabGroupComponent {
  // Single projected content
  readonly header = contentChild(TabHeaderComponent);

  // All projected children
  readonly tabs = contentChildren(TabComponent);
}
```

### 4e. `host` object — replaces `@HostBinding` / `@HostListener`

```typescript
@Component({
  selector: 'app-card',
  standalone: true,
  host: {
    // Bindings
    '[class.active]':   'isActive()',
    '[class.disabled]': 'disabled()',
    '[attr.role]':      '"article"',
    '[style.color]':    'color()',
    // Listeners
    '(click)':          'onClick($event)',
    '(keydown.enter)':  'onEnter()',
  },
  template: `<ng-content />`,
})
export class CardComponent {
  readonly isActive  = input(false);
  readonly disabled  = input(false);
  readonly color     = input('#000');
  onClick(e: MouseEvent) { ... }
  onEnter() { ... }
}
```

---

## 5. Template Syntax Reference

### 5a. Control flow (`@if` / `@for` / `@switch`)

```html
<!-- @if / @else if / @else -->
@if (loading()) {
  <mat-spinner />
} @else if (items().length === 0) {
  <p class="empty">No items</p>
} @else {
  <ul>...</ul>
}

<!-- @for — track is required -->
@for (item of items(); track item.id) {
  <app-item [data]="item" />
} @empty {
  <p>Nothing here</p>
}

<!-- @switch — v22: stacked cases + @default never (exhaustive) -->
@switch (status()) {
  @case ('pending')
  @case ('queued')  { <span class="pending">In queue</span>  }
  @case ('running') { <mat-spinner diameter="16" />          }
  @case ('done')    { <mat-icon>check</mat-icon>             }
  @case ('error')   { <mat-icon color="warn">error</mat-icon> }
  @default never;   <!-- compile-time exhaustiveness check    -->
}
```

### 5b. `@defer` — lazy template content

```html
<!-- Load when element enters viewport; prefetch when browser is idle -->
@defer (on viewport; prefetch on idle) {
  <app-insights-chart [data]="chartData()" />
} @placeholder (minimum 100ms) {
  <div class="chart-skeleton" style="height:320px"></div>
} @loading (after 150ms; minimum 300ms) {
  <mat-progress-bar mode="indeterminate" />
} @error {
  <p>Chart failed to load.</p>
}

<!-- Defer triggers -->
<!-- on idle            — browser requestIdleCallback          -->
<!-- on viewport        — IntersectionObserver                 -->
<!-- on interaction     — first click/keydown on placeholder   -->
<!-- on hover           — mouseenter on placeholder            -->
<!-- on timer(2s)       — fixed delay                          -->
<!-- when condition()   — when a signal/expression is truthy   -->
<!-- prefetch on idle   — pre-fetches JS chunk ahead of render -->
<!-- prefetch on hover  — pre-fetches on placeholder hover     -->
```

### 5c. New v22 template features

```html
<!-- Spread in [class] binding -->
<div [class]="{ ...baseCardStyles, 'selected': isSelected(), 'disabled': disabled() }">

<!-- Spread in array input -->
<app-list [items]="[...pinnedItems(), ...regularItems()]" />

<!-- Spread in function call -->
<p>{{ formatPrice(...priceArgs()) }}</p>

<!-- Arrow functions in event bindings -->
<button (click)="() => count.set(count() + 1)">+</button>
<input (input)="val => searchQuery.set(val.target.value)" />

<!-- Template comments (v22) -->
<!-- Note: this section only shows when user has budget set -->
@if (hasBudget()) {
  <app-budget-bar [data]="budget()" />
}
```

### 5d. Binding cheat-sheet

```html
<!-- Property binding -->
<img [src]="imageUrl()" [alt]="imageAlt()" />

<!-- Event binding -->
<button (click)="submit()">Save</button>

<!-- Two-way binding (signals) -->
<input [value]="name()" (input)="name.set($event.target.value)" />

<!-- Two-way with model() -->
<app-toggle [(checked)]="isDark" />

<!-- Class / style -->
<div [class.active]="isActive()" [style.color]="color()"></div>
<div [class]="{ active: isActive(), 'mat-elevation-z4': elevated() }"></div>

<!-- Attribute (for non-property HTML attrs) -->
<td [attr.colspan]="span()"></td>

<!-- Template reference -->
<input #nameInput />
<button (click)="nameInput.focus()">Focus</button>
```

---

## 6. Signal API Complete Reference

### 6a. Core signals

```typescript
import { signal, computed, effect, untracked, linkedSignal } from '@angular/core';

// ── signal() ──────────────────────────────────────────────────────────────
const count = signal(0);
count.set(5);                           // replace value
count.update(v => v + 1);              // derive from current
count.mutate(arr => arr.push(item));   // in-place mutation (arrays/objects)
const ro = count.asReadonly();          // Signal<number> — no set/update/mutate

// ── computed() ───────────────────────────────────────────────────────────
const double = computed(() => count() * 2);   // lazy + memoised
const full   = computed(() => `${first()} ${last()}`);

// Computed with equality override (skip re-render if semantically same)
const items = computed(() => fetchItems(), {
  equal: (a, b) => a.length === b.length && a[0]?.id === b[0]?.id,
});

// ── effect() ─────────────────────────────────────────────────────────────
// Runs immediately, re-runs when any read signal changes
effect(() => {
  console.log('count is', count());   // subscribes to count
});

// Effect with cleanup
effect(() => {
  const sub = socket.on('msg', handler);
  return () => sub.unsubscribe();     // cleanup on re-run or destroy
});

// Read signal without subscribing (untracked)
effect(() => {
  const x = count();
  const y = untracked(() => other());  // other changes won't re-trigger
  doSomething(x, y);
});

// ── linkedSignal() ───────────────────────────────────────────────────────
// Writable signal that resets when source changes
const options  = signal(['A', 'B', 'C']);
const selected = linkedSignal(() => options()[0]);  // resets to first on options change
selected.set('B');   // writable override
```

### 6b. Component signal APIs

```typescript
import { input, output, model, viewChild, viewChildren, contentChild, contentChildren } from '@angular/core';

// ── input() ──────────────────────────────────────────────────────────────
readonly title    = input<string>('');             // optional, default ''
readonly id       = input.required<string>();       // required — throws if missing
readonly count    = input(0, { transform: numberAttribute });
readonly label    = input<string>('', { alias: 'ariaLabel' });

// ── output() ─────────────────────────────────────────────────────────────
readonly clicked  = output<void>();
readonly selected = output<string>();
// Emit: this.selected.emit('foo');
// Listen: <app-x (selected)="onSelect($event)" />

// ── model() ──────────────────────────────────────────────────────────────
readonly value    = model<string>('');             // two-way: [(value)]="x"
readonly checked  = model.required<boolean>();     // required two-way

// ── viewChild() ──────────────────────────────────────────────────────────
readonly sort     = viewChild(MatSort);                              // Signal<MatSort | undefined>
readonly canvas   = viewChild.required<ElementRef>('canvasRef');     // Signal<ElementRef>
readonly allRows  = viewChildren(MatRow);                            // Signal<readonly MatRow[]>

// ── contentChild() ───────────────────────────────────────────────────────
readonly icon     = contentChild(MatIcon);                           // Signal<MatIcon | undefined>
readonly tabs     = contentChildren(TabComponent);                   // Signal<readonly TabComponent[]>
```

### 6c. Async signal APIs (v22 stable)

```typescript
import { resource } from '@angular/core';
import { rxResource } from '@angular/core/rxjs-interop';
import { httpResource } from '@angular/common/http';

// ── resource() — custom async loader ────────────────────────────────────
readonly post = resource({
  params: () => ({ id: this.postId() }),
  loader: async ({ params, abortSignal }) => {
    const res = await fetch(`/api/posts/${params.id}`, { signal: abortSignal });
    if (!res.ok) throw new Error(res.statusText);
    return res.json() as Post;
  },
});
// this.post.value()      → T | undefined
// this.post.status()     → 'idle' | 'loading' | 'refreshing' | 'resolved' | 'error' | 'local'
// this.post.isLoading()  → boolean
// this.post.error()      → unknown
// this.post.reload()     → force re-fetch
// this.post.set(value)   → override with local value (status = 'local')

// ── rxResource() — RxJS loader ───────────────────────────────────────────
readonly results = rxResource({
  params: () => ({ q: this.query() }),
  loader: ({ params }) => this.http.get<Result[]>('/api/search', {
    params: { q: params.q },
  }),
});

// ── httpResource() — typed HTTP GET ──────────────────────────────────────
readonly users = httpResource<User[]>(() => ({
  url: '/api/users',
  params: { filter: this.activeFilter() },
  headers: { 'X-Version': '2' },
}));

// Variants for non-JSON responses
httpResource.text(()        => ({ url: '/api/readme' }));
httpResource.blob(()        => ({ url: '/api/avatar' }));
httpResource.arrayBuffer(() => ({ url: '/api/binary' }));

// In template:
// @if (users.isLoading()) { <mat-spinner /> }
// @for (u of users.value() ?? []; track u.id) { ... }
```

### 6d. Experimental: `debounced()`

```typescript
import { debounced } from '@angular/core';

readonly query = signal('');

// Fixed delay
readonly debouncedQuery = debounced(this.query, 300);

// Dynamic delay (shorter for longer queries)
readonly debouncedQuery = debounced(this.query, (v) =>
  new Promise(resolve => setTimeout(resolve, v.length > 3 ? 150 : 400))
);

// Returns a Resource — has .value(), .isLoading()
// Use as reactive param to httpResource:
readonly results = httpResource(() => ({
  url: '/api/search',
  params: { q: this.debouncedQuery.value() ?? '' },
}));
```

### 6e. RxJS interop

```typescript
import { toSignal, toObservable, takeUntilDestroyed } from '@angular/core/rxjs-interop';

// Observable → Signal
// Must be called in injection context (field init or constructor)
readonly isMobile = toSignal(
  this.breakpoints.observe([Breakpoints.XSmall]).pipe(map(r => r.matches)),
  { initialValue: false },
);

// Signal → Observable
readonly query$ = toObservable(this.querySignal);

// Auto-unsubscribe when component destroys (replaces takeUntil + Subject pattern)
this.stream$.pipe(
  takeUntilDestroyed(),     // call in injection context
).subscribe(handler);

// With explicit ref outside injection context
private readonly destroyRef = inject(DestroyRef);
this.stream$.pipe(
  takeUntilDestroyed(this.destroyRef),
).subscribe(handler);
```

---

## 7. Change Detection

### How it works in Angular 22

Angular 22 ships **zoneless + OnPush by default**. Zone.js is no longer loaded by default for new apps.

| Trigger | Causes CD? |
|---|---|
| Signal in template changes | Yes — fine-grained, only that component |
| `async` pipe emits | Yes |
| Template event (`(click)`, etc.) | Yes |
| `markForCheck()` | Yes — schedules this view + ancestors |
| `ApplicationRef.tick()` | Yes — full app |
| `setTimeout` / `Promise` | **No** — zone.js not present |
| Direct field mutation (non-signal) | **No** |

### Rules
- **State that drives the template must be a signal** — plain fields do not trigger CD
- Use `signal()` for mutable state, `computed()` for derived, `effect()` for side-effects
- `ChangeDetectionStrategy.OnPush` is the default — do not set it explicitly for new components
- Legacy components that need the old behaviour: `changeDetection: ChangeDetectionStrategy.Eager`

---

## 8. Routing

### 8a. Route definitions — lazy-load everything

```typescript
// app.routes.ts
import { Routes } from '@angular/router';
import { authGuard } from '@core/auth/auth.guard';

export const routes: Routes = [
  // Public
  {
    path: 'login',
    loadComponent: () =>
      import('./features/auth/login.component').then(m => m.LoginComponent),
  },
  {
    path: 'auth/callback',
    loadComponent: () =>
      import('./core/auth/auth-callback.component').then(m => m.AuthCallbackComponent),
  },

  // Protected — shell wraps all authenticated routes
  {
    path: '',
    loadComponent: () =>
      import('./layout/shell.component').then(m => m.ShellComponent),
    canActivate: [authGuard],
    children: [
      { path: '', redirectTo: 'dashboard', pathMatch: 'full' },
      {
        path: 'dashboard',
        loadComponent: () =>
          import('./features/dashboard/dashboard.component').then(m => m.DashboardComponent),
      },
      {
        path: 'expenses',
        loadComponent: () =>
          import('./features/expenses/expenses.component').then(m => m.ExpensesComponent),
      },
      {
        path: 'categories',
        loadComponent: () =>
          import('./features/categories/categories.component').then(m => m.CategoriesComponent),
      },
      // Feature with sub-routes → loadChildren
      {
        path: 'settings',
        loadChildren: () =>
          import('./features/settings/settings.routes').then(m => m.settingsRoutes),
      },
    ],
  },

  { path: '**', redirectTo: 'login' },
];
```

### 8b. Functional guard

```typescript
// auth.guard.ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';

export const authGuard: CanActivateFn = () => {
  const auth   = inject(AuthService);
  const router = inject(Router);
  // Return true, UrlTree (redirect), or Observable/Promise of either
  return auth.isAuthenticated() ? true : router.createUrlTree(['/login']);
};
```

### 8c. Functional resolver

```typescript
export const postResolver: ResolveFn<Post> = (route) => {
  const posts = inject(PostsService);
  return posts.get(route.paramMap.get('id')!);
};

// In route:
{ path: 'post/:id', resolve: { post: postResolver }, loadComponent: ... }

// In component (with withComponentInputBinding()):
readonly post = input.required<Post>();   // injected from resolved data
```

### 8d. App config

```typescript
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideBrowserGlobalErrorListeners(),   // v22: replaces manual window.onerror

    provideRouter(routes,
      withComponentInputBinding(),                    // route params/data → input()
      withExperimentalPlatformNavigation(),           // v22 experimental: native Nav API
      withRouterConfig({ onSameUrlNavigation: 'reload' }),
    ),

    provideHttpClient(
      withInterceptors([tokenInterceptor]),
      // Note: withFetch() is REMOVED in v22 — Fetch is the default
    ),

    provideServiceWorker('ngsw-worker.js', {
      enabled: !isDevMode(),
      registrationStrategy: 'registerWhenStable:30000',
    }),
  ],
};
```

### Routing rules

| Rule | Why |
|---|---|
| `loadComponent()` for every route | Each route is its own JS chunk |
| `loadChildren()` for sub-route groups | One chunk for entire feature subtree |
| Functional guards with `inject()` | No class boilerplate, tree-shakeable |
| `withComponentInputBinding()` always | Route params flow to `input()` cleanly |
| Never lazy-load the first route the user hits | It must be immediate |
| `{ path: '**', redirectTo: ... }` last | Catch-all must be the final entry |

---

## 9. Dependency Injection

### 9a. `inject()` function — the only way in v22

```typescript
// In a component / service class body (field initialiser or constructor)
private readonly http    = inject(HttpClient);
private readonly router  = inject(Router);
readonly sheetConfig     = inject(SheetConfigService);   // public: template uses it

// With options
private readonly parent  = inject(ParentService, { optional: true });
private readonly self    = inject(SomeToken, { self: true });
private readonly skip    = inject(SomeToken, { skipSelf: true });

// In a functional guard / resolver (also an injection context)
export const authGuard: CanActivateFn = () => {
  const auth = inject(AuthService);
  ...
};
```

### 9b. Providing values

```typescript
// app.config.ts
providers: [
  // Value
  { provide: API_URL, useValue: 'https://api.example.com' },

  // Factory
  { provide: CONFIG, useFactory: () => ({ debug: isDevMode() }) },

  // Existing (alias)
  { provide: Logger, useExisting: ConsoleLogger },

  // Class override
  { provide: HttpBackend, useClass: XhrBackend },  // e.g. revert from Fetch

  // Environment injector (feature scope)
  { provide: FEATURE_TOKEN, useValue: 'expenses' },
]
```

### 9c. `InjectionToken`

```typescript
import { InjectionToken } from '@angular/core';

export const API_URL = new InjectionToken<string>('API_URL', {
  providedIn: 'root',
  factory: () => 'https://api.example.com',
});

// Inject:
private readonly apiUrl = inject(API_URL);
```

---

## 10. HTTP & Interceptors

### 10a. Functional interceptor (v15+, only pattern in v22)

```typescript
// token.interceptor.ts
import { HttpInterceptorFn, HttpErrorResponse } from '@angular/common/http';
import { inject } from '@angular/core';
import { catchError, throwError } from 'rxjs';

export const tokenInterceptor: HttpInterceptorFn = (req, next) => {
  const auth = inject(AuthService);
  const token = auth.accessToken();

  const authed = token
    ? req.clone({ setHeaders: { Authorization: `Bearer ${token}` } })
    : req;

  return next(authed).pipe(
    catchError((err: HttpErrorResponse) => {
      if (err.status === 401) auth.logout();
      return throwError(() => err);
    }),
  );
};

// Register:
provideHttpClient(withInterceptors([tokenInterceptor]))
```

### 10b. Direct HTTP with `firstValueFrom`

```typescript
// For one-shot calls where reactivity isn't needed
async getCategories(): Promise<Category[]> {
  const res = await firstValueFrom(
    this.http.get<{ values?: string[][] }>(
      `${this.baseUrl}/${this.sheetId}/values/Categories!A:E`
    )
  );
  return (res.values ?? []).slice(1).map(rowToCategory);
}
```

### 10c. Reactive HTTP with `httpResource`

```typescript
// For data that should re-fetch when params change
readonly expenses = httpResource<Expense[]>(() => ({
  url: `${this.baseUrl}/${this.sheetId()}/values/Expenses!A:G`,
}));
```

---

## 11. Signal Forms (v22 Stable)

### 11a. Basic form

```typescript
import { form, required, minLength, min, FormField } from '@angular/forms/signals';

@Component({
  imports: [FormField, MatFormFieldModule, MatInputModule, MatButtonModule],
  template: `
    <form (submit)="submit()">
      <mat-form-field>
        <mat-label>Name</mat-label>
        <input matInput [formField]="f.name" />
        @if (f.name().invalid() && f.name().touched()) {
          @for (err of f.name().errors(); track err.kind) {
            <mat-error>{{ err.message }}</mat-error>
          }
        }
      </mat-form-field>

      <button mat-flat-button type="submit" [disabled]="f.invalid()">Save</button>
    </form>
  `,
})
export class CategoryFormComponent {
  readonly model = signal({ name: '', color: '#4caf50', budgetLimit: 0 });

  readonly f = form(this.model, schema => {
    required(schema.name,       { message: 'Name is required'    });
    minLength(schema.name, 2,   { message: 'At least 2 characters' });
    min(schema.budgetLimit, 0,  { message: 'Must be 0 or more'  });
  });

  submit() {
    if (this.f.invalid()) return;
    this.dialogRef.close(this.model());
  }
}
```

### 11b. Async validation with debounce

```typescript
import { validateHttp } from '@angular/forms/signals';

readonly f = form(this.model, schema => {
  required(schema.email);
  validateHttp(schema.email, {
    debounce: 400,
    loader: (email) => this.http.get(`/api/check-email?email=${email}`).pipe(
      map(res => res.available ? null : [{ kind: 'taken', message: 'Email already in use' }])
    ),
  });
});
```

### 11c. Cross-field validation

```typescript
readonly f = form(this.model, (schema, root) => {
  required(schema.password);
  required(schema.confirmPassword);
  // Root-level validator with access to all fields
  validate(root, () => {
    const { password, confirmPassword } = root.value();
    if (password !== confirmPassword) {
      return [{ kind: 'mismatch', message: 'Passwords do not match' }];
    }
    return null;
  });
});
```

### When to use Signal Forms vs Reactive Forms

| Situation | Use |
|---|---|
| New form | Signal Forms |
| Working reactive form | Leave it — no forced migration |
| Dynamic `FormArray` of unknown length | Reactive Forms (Signal Forms handles this differently) |
| Third-party `ControlValueAccessor` library | Reactive Forms until library adds Signal Forms support |

---

## 12. Transloco i18n

### 12a. Setup

```typescript
// app.config.ts
@Service()
export class TranslocoHttpLoader implements TranslocoLoader {
  private readonly http = inject(HttpClient);
  getTranslation(lang: string) {
    // Files live in public/i18n/{lang}.json
    return this.http.get<Record<string, unknown>>(`i18n/${lang}.json`);
  }
}

provideTransloco({
  config: {
    availableLangs: ['en', 'fr', 'ar'],
    defaultLang: 'en',
    reRenderOnLangChange: true,   // re-renders templates on lang switch
    prodMode: !isDevMode(),
  },
  loader: TranslocoHttpLoader,
})
```

### 12b. In templates

```html
<!-- Structural directive — t is scoped translator function -->
<ng-container *transloco="let t">
  <h1>{{ t('dashboard.title') }}</h1>
  <p>{{ t('dashboard.welcome', { name: user()?.name }) }}</p>
  <button>{{ t('common.save') }}</button>
</ng-container>

<!-- Scoped prefix — t('amount_label') → expense_form.amount_label -->
<ng-container *transloco="let t; prefix: 'expense_form'">
  <mat-label>{{ t('amount_label') }}</mat-label>
  <mat-label>{{ t('date_label') }}</mat-label>
</ng-container>
```

### 12c. In services/signals — always `selectTranslate()`

```typescript
// WRONG — synchronous; fires before lang file loads → "Missing translation" warning
readonly title = computed(() => this.transloco.translate('nav.categories'));

// CORRECT — Observable; waits for lang file; use with toSignal()
readonly pageTitle = toSignal(
  this.routeSegment$.pipe(
    switchMap(segment => this.transloco.selectTranslate(`nav.${segment}`)),
    map(t => t || segment),
  ),
  { initialValue: 'dashboard' },
);

// CORRECT — in one-shot contexts after lang is loaded
const name = await firstValueFrom(this.transloco.selectTranslate('categories.default_food'));
```

### 12d. Language service pattern

```typescript
@Service()
export class LanguageService {
  private readonly transloco = inject(TranslocoService);

  readonly activeLang = signal<AppLang>(this.loadSaved());
  readonly languages  = LANGUAGES;

  constructor() {
    this.applyDom(this.activeLang());   // apply on boot

    effect(() => {
      const lang    = this.activeLang();
      const prevDir = document.documentElement.dir === 'rtl' ? 'rtl' : 'ltr';
      const nextDir = LANGUAGES.find(l => l.code === lang)!.dir;

      localStorage.setItem('et_lang', lang);

      if (prevDir !== nextDir) {
        // Angular Material CDK reads Directionality once at bootstrap
        // — direction changes MUST reload the page
        this.applyDom(lang);
        window.location.reload();
        return;
      }

      this.transloco.setActiveLang(lang);   // same direction: hot-swap
    });
  }

  setLang(code: AppLang): void { this.activeLang.set(code); }

  private applyDom(lang: AppLang): void {
    this.transloco.setActiveLang(lang);
    const dir = lang === 'ar' ? 'rtl' : 'ltr';
    document.documentElement.lang = lang;
    document.documentElement.dir  = dir;
    document.body.dir = dir;
    document.body.classList.toggle('rtl', dir === 'rtl');
  }
}
```

### 12e. Translation file conventions

```json
// public/i18n/en.json — scoped by feature
{
  "nav": {
    "dashboard": "Dashboard",
    "expenses": "Expenses",
    "categories": "Categories",
    "insights": "Insights",
    "settings": "Settings"
  },
  "dashboard": {
    "title": "Dashboard",
    "top_category_this_month": "Top category this month",
    "spent": "spent"
  },
  "categories": {
    "page_title": "Categories",
    "new_category": "New Category",
    "default_food": "Food & Dining",
    "default_transport": "Transport",
    "default_shopping": "Shopping"
  },
  "expense_form": {
    "amount_label": "Amount",
    "date_label": "Date",
    "category_required_error": "Category is required"
  }
}
```

**Rules:**
- One file per language: `public/i18n/{lang}.json`
- Keys scoped by feature: `feature.key_name`
- Add to **all** language files simultaneously
- Nested objects are fine but avoid > 2 levels
- Use `selectTranslate()` in TypeScript — never synchronous `translate()` on init

---

## 13. `@defer` — Template-Level Lazy Loading

```html
<!-- Supported triggers -->
@defer (on idle)          { ... }   <!-- requestIdleCallback                -->
@defer (on viewport)      { ... }   <!-- IntersectionObserver               -->
@defer (on interaction)   { ... }   <!-- first click/keydown on placeholder  -->
@defer (on hover)         { ... }   <!-- mouseenter on placeholder           -->
@defer (on timer(2000ms)) { ... }   <!-- fixed delay                        -->
@defer (when isOpen())    { ... }   <!-- signal/expression condition         -->

<!-- Combine triggers -->
@defer (on viewport; prefetch on idle) {
  <app-heavy-chart [data]="data()" />
} @placeholder (minimum 100ms) {
  <div class="skeleton" style="height: 280px; border-radius: var(--mat-sys-corner-medium)"></div>
} @loading (after 150ms; minimum 300ms) {
  <mat-progress-bar mode="indeterminate" />
} @error {
  <p class="mat-text-error">Failed to load component.</p>
}
```

**When to use `@defer`:**
- Components > 50 KB minified
- Content below the fold (charts, insights, heavy tables)
- Components that are only shown conditionally (settings panels, modals)
- Third-party libraries (PDF viewers, rich text editors)

---

## 14. Dialog Pattern

### 14a. Opening a dialog

```typescript
private readonly dialog = inject(MatDialog);

openEditDialog(category: Category): void {
  this.dialog
    .open(CategoryFormComponent, {
      data: { category } satisfies CategoryFormData,   // typed via interface
      width: '480px',
      maxWidth: '95vw',
      maxHeight: '90vh',
    })
    .afterClosed()
    .subscribe((result: Category | undefined) => {
      if (!result) return;
      this.categories.update(list =>
        list.map(c => c.id === result.id ? result : c)
      );
    });
}
```

### 14b. Dialog component

```typescript
export interface CategoryFormData {
  category?: Category;
}

@Component({
  selector: 'app-category-form',
  standalone: true,
  imports: [MatDialogModule, MatFormFieldModule, MatInputModule, MatButtonModule, TranslocoModule],
  template: `
    <ng-container *transloco="let t">
      <h2 mat-dialog-title>
        {{ isEdit ? t('category_form.edit_title') : t('category_form.new_title') }}
      </h2>
      <mat-dialog-content>
        <mat-form-field appearance="outline">
          <mat-label>{{ t('category_form.name_label') }}</mat-label>
          <input matInput [value]="name()" (input)="onNameInput($event)" maxlength="40" />
        </mat-form-field>
      </mat-dialog-content>
      <mat-dialog-actions align="end">
        <button mat-button mat-dialog-close>{{ t('common.cancel') }}</button>
        <button mat-flat-button [disabled]="!isValid()" (click)="submit()">
          {{ t('common.save') }}
        </button>
      </mat-dialog-actions>
    </ng-container>
  `,
})
export class CategoryFormComponent {
  readonly data      = inject<CategoryFormData>(MAT_DIALOG_DATA);
  readonly dialogRef = inject(MatDialogRef<CategoryFormComponent>);

  readonly isEdit = !!this.data.category;
  readonly name   = signal(this.data.category?.name ?? '');
  readonly isValid = computed(() => this.name().trim().length >= 2);

  onNameInput(e: Event): void {
    this.name.set((e.target as HTMLInputElement).value);
  }

  submit(): void {
    if (!this.isValid()) return;
    const result: Category = {
      ...(this.data.category ?? { id: crypto.randomUUID(), color: '#4caf50', budgetLimit: 0, icon: 'label' }),
      name: this.name().trim(),
    };
    this.dialogRef.close(result);
  }
}
```

---

## 15. Performance Checklist

| Item | Implementation |
|---|---|
| Signals for all mutable state | `signal()` — fine-grained updates, no zone needed |
| Derived state via `computed()` | Lazy, memoised — re-computes only when deps change |
| OnPush default | Automatic in v22; don't set explicitly |
| Lazy-load all routes | `loadComponent()` / `loadChildren()` for every route |
| Template lazy-loading | `@defer (on viewport)` for below-fold content |
| Lazy-load heavy services | `injectAsync()` for services not needed at boot |
| Reactive HTTP | `httpResource()` — eliminates manual signal wiring |
| Debounced search | `debounced(query, 300)` (experimental) |
| Optimised images | `NgOptimizedImage` + `priority` on LCP image |
| `trackBy` in loops | `@for (item of items(); track item.id)` — required |
| Avoid template function calls | Use `computed()` instead of `fn()` in templates |
| Bundle analysis | `ng build --stats-json` + `webpack-bundle-analyzer` |
| `takeUntilDestroyed()` | Auto-clean RxJS subscriptions in components |

---

## 16. Do / Don't — Quick Reference

| Do | Don't |
|---|---|
| `@Service()` for singletons | `@Injectable` for new services |
| `inject()` at field level | Constructor parameter injection with `@Service` |
| `input()` / `output()` / `model()` | `@Input()` / `@Output()` decorators |
| `viewChild()` / `viewChildren()` | `@ViewChild()` / `@ViewChildren()` decorators |
| `host: { '[class.x]': 'sig()' }` in decorator | `@HostBinding()` decorator |
| `host: { '(click)': 'fn()' }` in decorator | `@HostListener()` decorator |
| `standalone: true` (default in v22) | NgModules for new code |
| `loadComponent()` for every route | Eager-loading all routes |
| `@if` / `@for` / `@switch` | `*ngIf` / `*ngFor` / `*ngSwitch` |
| `@defer (on viewport)` for heavy content | Eagerly rendering everything |
| `selectTranslate()` in services | `translate()` in constructor / field init |
| `httpResource()` for reactive HTTP | `toSignal(http.get(...))` wiring |
| Signal Forms for new forms | New `FormGroup` / `FormControl` |
| `computed()` for derived state | Deriving state inside `effect()` |
| `asReadonly()` on exposed signals | Exposing writable signals publicly |
| `protected` for template-only members | `public` for template-only members |
| `track item.id` in `@for` | `track $index` (unstable identity) |
| `numberAttribute` / `booleanAttribute` transform | Manual string parsing in `input()` |

---

## 17. When to Invoke This Skill

Invoke automatically when:
- Creating a new service — apply `@Service()` + `inject()` + signal patterns
- Creating a new component — standalone, signals, correct imports
- Adding a new route — lazy `loadComponent()` + guard
- Writing a new form — Signal Forms or noting when to use Reactive
- Adding translation keys — remind about all lang files + `selectTranslate()`
- Reviewing change detection — ensure state is signals, not plain fields
- Choosing between `resource()` / `rxResource()` / `httpResource()`
- Adding a dialog — typed `MAT_DIALOG_DATA` + signal form state
- Optimising performance — `@defer`, `httpResource`, `injectAsync`, `trackBy`
- Fixing "Missing translation" warnings — replace `translate()` with `selectTranslate()`
- Fixing NG0951 errors — use `viewChild()` (not `.required()`) + guard in `effect()`
