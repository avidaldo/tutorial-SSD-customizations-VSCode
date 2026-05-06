# AGENTS

This repository is intended to host a backend microservice for a Task Management application.

Task API Copilot Instructions

Project intent:

- This repository follows specs-driven development (SDD).
- Treat files in `docs/specs/` as the source of truth for feature behavior.
- If requested behavior is not in a specification, ask before implementing it.

Current repository state:

- There is no application source code, package manifest, or runtime configuration yet.
- Treat `tutorial/` as non-product reference material. Do not use it as implementation context for backend changes unless a user explicitly asks for tutorial work.

Default stack assumptions:

- Node.js with TypeScript.
- RESTful HTTP API.
- Task domain with CRUD operations and strict state transitions.

Technology choices:

- Use Node.js and TypeScript for backend code.
- Prefer small modules and explicit types.
- Keep HTTP routing, validation, and business logic separated.

Working rules for agents:

- Confirm assumptions before introducing project structure that is not already present in the repository.
- Prefer minimal, production-oriented scaffolding over demo code.
- Keep business rules for task state transitions centralized in one domain/service layer rather than spreading them across routes, controllers, or persistence code.
- Validate every state change against an explicit transition map or state machine.
- Keep request validation, domain logic, and persistence concerns separated.
- Favor small focused modules and predictable naming.

Implementation rules:

- Do not implement a feature until the relevant spec file has been read.
- Every function, route handler, controller, or service that directly fulfills a requirement must include a JSDoc block with `@spec <requirement-id>`.
- If a specification is ambiguous, stop and ask clarifying questions instead of guessing.
- Prefer minimal, traceable changes over broad scaffolding.

Expected backend shape once code exists:

- `src/routes` or `src/controllers` for HTTP wiring only.
- `src/services` or `src/domain` for task lifecycle logic.
- `src/repositories` for data access.
- `src/schemas` or `src/validators` for request and response validation.
- `src/types` for shared TypeScript contracts when needed.

Task domain guidance:

- Model task status as a constrained enum or union type.
- Reject invalid transitions with a clear client error.
- Cover state transition rules with focused tests before widening API behavior.
- Preserve invariants during update operations; partial updates must not bypass transition validation.

API guidance:

- Keep handlers thin and deterministic.
- Return stable JSON shapes and explicit HTTP status codes.
- Treat create, update, transition, and delete flows as separate behaviors for testing.
- If a dedicated transition endpoint exists, keep transition validation there and in the underlying domain layer.

Validation and tooling:

- Once project commands exist, prefer the narrowest relevant validation first: targeted tests, then typecheck, then lint.
- Document canonical setup, run, test, and build commands here or in repository docs when they are added.

Testing rules:

- Use Jest for unit tests.
- Add at least one test for each edge case named in a specification.
- Mock external I/O in unit tests.

If you scaffold this service later, update this file with:

- Actual package manager and scripts.
- Persistence choice.
- Environment variable contract.
- Concrete test and build commands.
