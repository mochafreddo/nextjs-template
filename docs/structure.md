# Project Structure

This template uses the Next.js App Router and keeps the codebase intentionally minimal.
The structure is designed to make incremental growth predictable: start small, add tooling and features step by step, and document each decision along the way.

## Top-level layout

```
src/
  app/
    layout.tsx
    page.tsx
    globals.css
    page.module.css
    favicon.ico
  components/
  lib/
  styles/
docs/
public/
```

- `src/` contains all application source code.
- `docs/` records the rationale behind defaults as the template evolves.
- `public/` holds static assets served directly by Next.js.

## Why we use `src/`

We place all runtime code under `src/` for clarity and scalability.

- **Separation of concerns:** configuration files (`next.config.mjs`, `eslint.config.mjs`, `tsconfig.json`) stay at the repo root, while app code lives in a single, predictable place.
- **Cleaner root:** as the template grows (scripts, CI files, tooling), the root directory stays readable instead of becoming a mix of code and config.
- **Industry-standard default:** many mature Next.js codebases follow this pattern, which reduces onboarding friction.

Next.js supports `src/` natively, so this adds no runtime cost.

## `app/`: routing only

`src/app/` is the App Router root. It should contain only route-level files:

- pages and layouts (`page.tsx`, `layout.tsx`)
- route groups and segments (`(marketing)/`, `[id]/`)
- loading/error boundaries (`loading.tsx`, `error.tsx`, `not-found.tsx`)
- route-local styles when appropriate (e.g., `page.module.css`)

**Rule of thumb:** if a file exists because of routing, it belongs in `app/`.
If a file exists because of UI reuse or business logic, it does not.

## `components/`: reusable UI

`src/components/` holds shared React components that are not tied to a specific route.

Examples:

- UI primitives (Button, Input, Modal)
- layout building blocks (Header, Footer, Sidebar)
- form elements, icons, theme toggles

This keeps the routing tree focused and makes components easy to discover and reuse.

## `lib/`: non-UI logic

`src/lib/` is for code that is not a React component.

Examples:

- API clients and fetch helpers
- domain utilities (date, currency, validation)
- auth/session helpers
- constants and types shared across features

Keeping logic separate from UI reduces circular dependencies and keeps imports clean.

## `styles/`: shared styling assets

`src/styles/` is reserved for styling that is shared across routes and components.

Examples:

- design tokens (colors, spacing)
- global CSS modules or reset files
- Tailwind/Vanilla Extract style utilities
- theme definitions

Global CSS entry points still live under `app/` per Next.js conventions (e.g., `app/globals.css`).
As styling grows, shared assets can live in `styles/` and be imported from `app/globals.css`.

## Alternatives considered

We considered a few common layouts before choosing this one:

1. **No `src/` (root-level `app/`)**
   - Pros: matches Next.js defaults, one fewer directory.
   - Cons: the root becomes noisy quickly as tooling and scripts accumulate.
   - Decision: rejected in favor of long-term clarity.

2. **Placing components under `app/`**
   - Pros: everything under the routing tree.
   - Cons: mixes route-level concerns with reusable UI; navigation becomes harder as the app scales.
   - Decision: rejected to keep `app/` routing-only.

3. **Single `shared/` folder**
   - Pros: fewer top-level directories.
   - Cons: unclear boundary between UI and logic; “where should this go?” becomes ambiguous.
   - Decision: rejected in favor of explicit separation (`components/` vs `lib/`).

4. **Domain/feature-first structure (`features/`, `modules/`)**
   - Pros: great for large, domain-heavy products.
   - Cons: too opinionated for a starter template where domains vary per project.
   - Decision: deferred; teams can adopt this later if needed.

## In practice

This structure is a default, not a constraint. The goal is to provide a clean starting point with clear rules, so the template can evolve incrementally without rewrites.
