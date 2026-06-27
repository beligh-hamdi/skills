# beligh-hamdi/skills

Agent skills for Angular development.

[![skills.sh](https://skills.sh/b/beligh-hamdi/skills)](https://skills.sh/beligh-hamdi/skills)

## Available Skills

### angular22

Angular 22 complete reference — signals, `@Service()`, standalone components, project structure
(core/features/shared), lazy routing, Transloco i18n, Signal Forms, resource APIs, change
detection, template syntax, and every new v22 API.

Sourced directly from [angular.dev](https://angular.dev) and [blog.angular.dev](https://blog.angular.dev/angular-v22).

**Use when:**
- Building or reviewing Angular 22 components, services, or routes
- Migrating from `@Injectable` to the new `@Service()` decorator
- Setting up Signal Forms or using `resource()` / `httpResource()` APIs
- Configuring lazy routing, Transloco i18n, or OnPush change detection
- Applying the core/features/shared project structure
- Using new v22 APIs such as `injectAsync()`, `debounced()`, or template spread syntax

### angular-material-m3

Angular Material 22 + Material Design 3 — complete reference for theming, system tokens,
utility classes, component overrides, typography mixins, and accessibility.

Sourced directly from [material.angular.dev](https://material.angular.dev) and [m3.material.io](https://m3.material.io).

**Use when:**
- Writing or reviewing Angular components with Material Design 3
- Setting up or customising a `mat.theme()` palette and typography
- Choosing the right `--mat-sys-*` colour role token for a UI element
- Applying M3-compliant typography with `mat.typography-*()` mixins
- Auditing a codebase for hardcoded colours or border-radius values
- Implementing accessible focus indicators or disabled states

## Install

```bash
# Install all skills
npx skills add beligh-hamdi/skills

# Install a specific skill
npx skills add beligh-hamdi/skills@angular22
npx skills add beligh-hamdi/skills@angular-material-m3
```
