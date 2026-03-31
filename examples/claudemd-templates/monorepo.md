# Monorepo CLAUDE.md Template

Use nested CLAUDE.md files for monorepos. Each package gets its own context.

---

## Root CLAUDE.md

```markdown
# MyMonorepo

Monorepo with [N] packages managed by [pnpm workspaces / Turborepo / Nx].

## Structure
- `packages/api/` — REST API service
- `packages/web/` — Next.js frontend
- `packages/shared/` — shared types and utilities
- `packages/db/` — database client and migrations

## Global Commands
- `pnpm install` — install all dependencies
- `pnpm build` — build all packages
- `pnpm test` — test all packages
- `pnpm lint` — lint all packages

## Conventions
- Shared types: import from `@myorg/shared`
- Database access: import from `@myorg/db`
- New packages: use `pnpm create-package <name>`
- All packages use TypeScript strict mode
```

## packages/api/CLAUDE.md

```markdown
# API Package

REST API built with Fastify + TypeScript.

## Commands
- `pnpm dev` — start dev server (port 4000)
- `pnpm test` — run tests with Vitest

## Structure
- `src/routes/` — route handlers (auto-loaded by Fastify)
- `src/plugins/` — Fastify plugins
- `src/schemas/` — Zod validation schemas

## Notes
- Routes are auto-registered from filesystem
- Use `@myorg/db` for all database operations
- Auth middleware in `src/plugins/auth.ts`
```

## packages/web/CLAUDE.md

```markdown
# Web Package

Next.js 14 frontend with App Router.

## Commands
- `pnpm dev` — start dev server (port 3000)
- `pnpm test` — run tests with Vitest + Testing Library

## Structure
- `src/app/` — pages and layouts (App Router)
- `src/components/` — React components
- `src/lib/` — utilities and API client

## Notes
- Server Components by default, add 'use client' only when needed
- API calls: use functions in `src/lib/api.ts`
- Types: import from `@myorg/shared`
```

---

**Why nested CLAUDE.md works**: When Claude works in `packages/api/`, it loads both root and api CLAUDE.md. This gives relevant context without loading info about unrelated packages.
