# Architecture: [PROJECT NAME]

> Architectural style, layer organization, data flow, and key design decisions.

---

## Architectural Style

<!-- e.g., Layered (N-tier), Hexagonal, Event-Driven, Microservices, Monolith -->

[Describe the architectural style and the rationale behind the choice.]

---

## Layer Organization

```
[project-root]/
├── src/
│   ├── [layer-1]/       # e.g., domain — business rules and entities
│   ├── [layer-2]/       # e.g., application — use cases and orchestration
│   ├── [layer-3]/       # e.g., infrastructure — DB, queues, external APIs
│   └── [layer-4]/       # e.g., interface — HTTP controllers, CLI, consumers
└── test/
    ├── unit/
    ├── integration/
    └── e2e/
```

---

## Data Flow

```
[Describe request/event flow from entry point to persistence and back]

Example:
HTTP Request → Controller → Use Case → Domain → Repository → Database
                                     ↑
                              Domain Events → Event Bus → Consumers
```

---

## Key Components

| Component | Layer | Responsibility |
|-----------|-------|----------------|
| [e.g., UserController] | Interface | Receives HTTP, delegates to use case |
| [e.g., CreateUserUseCase] | Application | Orchestrates domain + persistence |
| [e.g., User] | Domain | Entity with business rules |
| [e.g., UserRepository] | Infrastructure | Prisma persistence adapter |

---

## Dependency Direction

<!-- Dependencies must only point inward (toward domain). -->

```
Interface → Application → Domain ← Infrastructure
```

---

## Cross-Cutting Concerns

| Concern | Approach |
|---------|----------|
| Error Handling | [e.g., Result pattern / exceptions] |
| Logging | [e.g., Pino with structured JSON] |
| Validation | [e.g., Zod at interface layer] |
| Authentication | [e.g., JWT middleware] |
| Authorization | [e.g., RBAC in application layer] |

---

## Architecture Decisions (ADR Summary)

| ID | Decision | Rationale |
|----|----------|-----------|
| ADR-001 | [e.g., Hexagonal over MVC] | [reason] |

---

## Constraints & Risks

<!-- Known architectural limitations or technical debt related to architecture -->

-
