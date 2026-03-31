# Writing Effective CLAUDE.md Files

CLAUDE.md is a markdown file at your project root that Claude Code reads at the start of every session. It's your most powerful tool for reducing repetitive token usage.

## Why CLAUDE.md Saves Tokens

Without CLAUDE.md, you repeat project context every session:
```
"This is a Next.js app with Prisma ORM. Use TypeScript strict mode.
 Tests are in __tests__/. Run with npm test. Database is PostgreSQL..."
```

That's 50-200 tokens per session × dozens of sessions = thousands of wasted tokens.

With CLAUDE.md, this context is loaded automatically and **cached** (10x cheaper than typing it manually).

## Structure

```markdown
# Project Name

Brief one-line description.

## Tech Stack
- Framework: Next.js 14 (App Router)
- Language: TypeScript (strict mode)
- ORM: Prisma with PostgreSQL
- Testing: Jest + React Testing Library

## Commands
- `npm run dev` — start dev server
- `npm test` — run tests
- `npm run build` — production build
- `npx prisma migrate dev` — run migrations

## Project Structure
- `src/app/` — Next.js pages and API routes
- `src/lib/` — shared utilities
- `src/components/` — React components
- `prisma/` — database schema and migrations

## Conventions
- Use named exports (not default)
- Error handling: return Result types, don't throw
- Database access: always use `src/lib/db.ts`
- API responses: use `src/lib/response.ts` helpers
```

## What to Include

### Always include:
- **Tech stack** — framework, language, key libraries
- **Common commands** — test, build, lint, migrate
- **Project structure** — where to find what
- **Coding conventions** — anything non-obvious

### Include if relevant:
- **Architecture decisions** — why things are done a certain way
- **Known quirks** — gotchas that would waste time discovering
- **File naming patterns** — helps Claude find files faster

### Don't include:
- Full API documentation (too many tokens)
- Complete file listings (Claude can explore)
- Things that change frequently (becomes outdated)

## Hierarchy

Claude Code supports multiple CLAUDE.md files:

```
project/
├── CLAUDE.md              ← project-wide context
├── src/
│   └── CLAUDE.md          ← src-specific context
├── packages/
│   ├── api/
│   │   └── CLAUDE.md      ← API package context
│   └── web/
│       └── CLAUDE.md      ← Web package context
```

Nested CLAUDE.md files are loaded when Claude works in those directories. Use this in monorepos.

## Anti-Patterns

### Too long
```markdown
# Bad: 500+ lines of documentation
[Full API reference pasted here]
[Complete database schema]
[All environment variables]
```

This wastes tokens on every session. Link to docs instead.

### Too vague
```markdown
# Bad: Doesn't help Claude do anything
This is a web application. It uses JavaScript.
```

### Outdated
```markdown
# Bad: Stale information causes wrong actions
Database: MongoDB    ← Actually migrated to PostgreSQL 3 months ago
```

Keep CLAUDE.md current. Outdated instructions waste tokens on wrong approaches.

## Templates

See the [templates directory](../../examples/claudemd-templates/) for ready-to-use examples.

## Next Steps

- [Cost Monitoring](11-cost-monitoring.md) — track the impact of your CLAUDE.md
- [Practical Tips](08-practical-tips.md) — more optimization strategies
