# Environment Variables Strategy

This template treats environment variables as part of the application contract. The goal is to keep secrets safe by default, make public exposure explicit, and enforce type safety at runtime.

## Principles

1. **Server-only by default**
   - Any value that can grant access or reveal sensitive data must remain server-only.
   - Examples: database URLs, API keys, auth secrets, private feature flags.

2. **Public values are explicit**
   - Only variables prefixed with `NEXT_PUBLIC_` are exposed to the client bundle.
   - Treat all `NEXT_PUBLIC_*` values as readable by anyone who opens DevTools.

3. **Fail fast**
   - Missing or invalid variables should crash early (both in dev and production).
   - Do not silently fall back for required secrets.

4. **Document all variables**
   - Every required env var must be listed in `.env.example` (keys + brief notes only).
   - Never commit real secrets.

## Supported `.env*` files and precedence

Next.js loads `.env*` automatically in local development.

Priority (highest → lowest):

1. `.env.local` (not committed)
2. `.env.development` / `.env.production`
3. `.env`

Recommended usage:

- `.env` — shared defaults that are safe for all environments.
- `.env.development` — development-only defaults.
- `.env.local` — per-developer overrides and secrets.
- Production secrets — injected via your hosting platform (e.g., Vercel, CI, Secrets Manager), not via committed files.

## Naming rules

- **Server-only:** `UPPER_SNAKE_CASE`
  - Example: `DATABASE_URL`, `AUTH_SECRET`, `STRIPE_SECRET_KEY`
- **Client-exposed:** `NEXT_PUBLIC_UPPER_SNAKE_CASE`
  - Example: `NEXT_PUBLIC_APP_URL`, `NEXT_PUBLIC_SENTRY_DSN`

Never prefix secrets with `NEXT_PUBLIC_`.

## Type safety and runtime validation

Use schema validation at startup to make env usage safe and predictable.

Recommended structure:

```text
src/lib/env/
  server.ts   # server-only schema
  client.ts   # client-only schema (NEXT_PUBLIC_* only)
```

### Server schema

```ts
// src/lib/env/server.ts
import { z } from "zod";

const serverSchema = z.object({
  DATABASE_URL: z.string().url(),
  AUTH_SECRET: z.string().min(1),
});

export const envServer = serverSchema.parse(process.env);
```

### Client schema

```ts
// src/lib/env/client.ts
import { z } from "zod";

const clientSchema = z.object({
  NEXT_PUBLIC_APP_URL: z.string().url(),
});

export const envClient = clientSchema.parse({
  NEXT_PUBLIC_APP_URL: process.env.NEXT_PUBLIC_APP_URL,
});
```

Notes:

- Client env is parsed from a **picked object**, not the full `process.env`.
- If a value is optional, mark it optional in the schema and handle it explicitly in code.

## Usage rules

- **Server Components / Route Handlers / Server Actions**
  - Import from `envServer`.
  - Do not access `process.env` directly outside the env module.

```ts
import { envServer } from "@/lib/env/server";

export async function GET() {
  const url = envServer.DATABASE_URL;
  // ...
}
```

- **Client Components**
  - Import from `envClient`.
  - Only use `NEXT_PUBLIC_*` variables.

```ts
"use client";
import { envClient } from "@/lib/env/client";

export function Footer() {
  return <a href={envClient.NEXT_PUBLIC_APP_URL}>Home</a>;
}
```

Forbidden patterns:

- Accessing `process.env.X` directly in components.
- Exposing secrets by adding `NEXT_PUBLIC_`.

Allowed pattern:

- Read secret values on the server, then pass only the minimum needed to the client via props or API responses.

## `.env.example`

Keep a committed `.env.example` at the repo root:

```dotenv
# Server-only
DATABASE_URL=
AUTH_SECRET=

# Client-exposed
NEXT_PUBLIC_APP_URL=
```

Update checklist when adding a new variable:

1. Add key to `.env.example` with a short comment.
2. Add to `src/lib/env/server.ts` or `src/lib/env/client.ts`.
3. Use via `envServer` / `envClient` only.
4. Configure in hosting/CI for production.

## Deployment notes

- Do not rely on `.env.local` in production.
- Rotate secrets regularly; treat env vars as credentials.
- If you need environment-specific public values, define them per environment in your hosting platform (still with `NEXT_PUBLIC_`).
