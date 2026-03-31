# Full-Stack Project CLAUDE.md Template

Use this for full-stack web applications with frontend + backend + database.

---

```markdown
# MyApp

Full-stack web application for [purpose].

## Tech Stack

### Backend
- Runtime: Node.js 20 / Bun
- Framework: Express / Fastify
- Language: TypeScript (strict mode)
- ORM: Prisma
- Database: PostgreSQL
- Auth: JWT + bcrypt
- Validation: Zod

### Frontend
- Framework: React 18 / Next.js 14
- Styling: Tailwind CSS
- State: Zustand / React Query
- Forms: React Hook Form + Zod

### Infrastructure
- Docker Compose for local dev
- CI: GitHub Actions (.github/workflows/)

## Commands

### Backend
- `cd backend && npm run dev` — start API server (port 3001)
- `cd backend && npm test` — run backend tests
- `cd backend && npx prisma migrate dev` — run migrations
- `cd backend && npx prisma studio` — database GUI

### Frontend
- `cd frontend && npm run dev` — start frontend (port 3000)
- `cd frontend && npm test` — run frontend tests
- `cd frontend && npm run build` — production build

### Full Stack
- `docker compose up` — start everything
- `npm run lint` — lint all packages

## Project Structure
- `backend/src/routes/` — API route handlers
- `backend/src/services/` — business logic
- `backend/src/middleware/` — Express middleware
- `backend/prisma/` — database schema and migrations
- `frontend/src/pages/` — page components
- `frontend/src/components/` — reusable UI components
- `frontend/src/hooks/` — custom React hooks
- `frontend/src/api/` — API client functions

## Conventions
- API responses: `{ data: T }` for success, `{ error: string }` for errors
- Use named exports everywhere
- Backend errors: throw AppError from `backend/src/utils/errors.ts`
- Frontend API calls: use functions in `frontend/src/api/`
- Database: never query directly, use service layer
- Tests: colocated with source files as `*.test.ts`
```

---

**Token cost**: ~200-300 tokens per session. Worth it for the consistency it provides.
