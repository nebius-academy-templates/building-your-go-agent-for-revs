---
name: architecture-guidelines
description: "Team architecture for Go services: layers, repository data access, centralized configuration, and constructor injection. Load for architecture reviews."
---

These are this team's architecture decisions (see `docs/adr/ADR-001` through `ADR-004`), not universal Go language requirements.

## Layered architecture

- Handlers and `cmd/` entrypoints parse input and format output.
- Services contain business logic.
- Entrypoints must not query persistence directly.
- Findings must be supported by evidence from changed code.

## Configuration

- Runtime configuration and secrets are loaded centrally in `internal/config` or in the composition root, and passed through constructors.
- Services must not call `os.Getenv` ad hoc.
- Services must not contain embedded credentials.
- Ordinary algorithm constants are allowed.
- A hardcoded credential is primarily a `[HIGH]` security finding; an architecture-consolidation finding must not duplicate it.

## Repository pattern

- Services obtain data through small repository interfaces, normally declared near the consumer.
- Concrete adapters in `internal/repository` may use `internal/api` or `database/sql`.
- Services must not import or call API clients or SQL directly.
- Injecting a concrete API client satisfies dependency injection but still bypasses the repository layer.
- A normal repository-boundary violation is `[MEDIUM]`.
- Tests may connect concrete adapters to local `httptest` servers.

## Dependency injection

- Dependencies are passed through `NewXxx` constructors or function parameters.
- Use small interfaces when a consumer needs substitution.
- Do not use global mutable service instances, global clients, or ServiceLocator registries.
- Tests may provide fakes or local adapters.
- This is a team decision, not a Go language constraint.
