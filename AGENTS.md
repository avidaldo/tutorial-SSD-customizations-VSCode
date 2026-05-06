# Task Management API — Agent Instructions

A Node.js + TypeScript backend microservice providing a RESTful API for task management.

## Architecture

Strict three-layer separation of concerns. Never mix responsibilities across layers:

| Layer | Location | Responsibility |
|-------|----------|----------------|
| **Routes** | `src/routes/` | HTTP binding, input validation, calling controllers |
| **Services** | `src/services/` | Business logic, state machine, authorization checks |
| **Repositories** | `src/repositories/` | All data access (DB queries, ORM calls) — no logic |

Controllers (if used) sit between routes and services. Services must **not** import from routes; repositories must **not** import from services.

## Key Domain Rules

- **Task state machine** — valid transitions only: `To-Do → In-Progress → Done`. Reject invalid transitions in the service layer with a `400` response.
- **RBAC** — enforce role checks in middleware/service; roles: `admin`, `member` (define in `src/types/roles.ts` or similar).
- **JWT authentication** — tokens issued/verified in a dedicated `auth` service; never inline JWT logic in routes or other services.

## Conventions

- Language: TypeScript strict mode (`"strict": true` in `tsconfig.json`)
- Error handling: centralized error middleware in `src/middleware/errorHandler.ts`; throw typed custom errors from services
- DTOs / request validation: use a validation library (e.g. `zod` or `class-validator`) at the route layer before reaching services
- Environment config: all secrets and config via environment variables; use a `src/config/` module to load and export them — never use `process.env` directly in business logic
- No business logic in route handlers; no DB calls outside repositories

## Common Commands

> Fill in once the project is initialized:

```bash
# Install dependencies
npm install

# Development (with hot reload)
npm run dev

# Build
npm run build

# Run tests
npm test

# Lint
npm run lint
```

## Project Structure (target)

```
src/
  config/         # env vars, app configuration
  routes/         # Express routers — HTTP only
  services/       # Business logic, domain rules
  repositories/   # Data access layer
  middleware/     # Auth, RBAC, error handling, validation
  types/          # Shared TypeScript types, enums (roles, task states)
  utils/          # Pure utility functions
tests/
  unit/
  integration/
```
