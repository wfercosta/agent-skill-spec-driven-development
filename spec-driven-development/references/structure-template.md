# Structure: [PROJECT NAME]

> Directory layout, module organization, and key file locations.

---

## Root Layout

```
[project-root]/
├── src/                    # Application source code
├── test/                   # Test files (if separate from src)
├── .specs/                 # Spec-driven development artifacts
├── docs/                   # Additional documentation (optional)
├── scripts/                # Utility scripts (build, seed, etc.)
├── docker/                 # Docker configuration files
├── [config files]          # tsconfig.json, package.json, .eslintrc, etc.
└── README.md
```

---

## Source Directory (`src/`)

```
src/
├── [module-1]/             # [Describe purpose, e.g., domain entities]
│   ├── [entity].ts
│   └── index.ts            # Public API of the module
│
├── [module-2]/             # [Describe purpose, e.g., use cases]
│   ├── [use-case].ts
│   └── index.ts
│
├── [module-3]/             # [Describe purpose, e.g., infrastructure adapters]
│   ├── [adapter].ts
│   └── index.ts
│
├── [module-4]/             # [Describe purpose, e.g., HTTP controllers]
│   ├── [controller].ts
│   └── index.ts
│
├── shared/                 # Shared utilities, types, errors
│   ├── errors.ts
│   ├── types.ts
│   └── utils.ts
│
└── main.ts                 # Application entry point
```

---

## Test Directory (`test/`)

```
test/
├── unit/                   # Pure unit tests (no I/O)
│   └── [mirrors src/ structure]
│
├── integration/            # Tests with real dependencies (DB, queues)
│   └── [feature or module]
│
└── e2e/                    # Full end-to-end tests
    └── [scenario]
```

---

## Key Files

| File | Purpose |
|------|---------|
| `src/main.ts` | Application bootstrap |
| `src/shared/errors.ts` | Domain error definitions |
| `src/shared/types.ts` | Shared type definitions |
| `tsconfig.json` | TypeScript configuration |
| `package.json` | Dependencies and scripts |
| `.env.example` | Environment variable documentation |
| `docker-compose.yml` | Local development services |

---

## Module Boundaries

Each module exposes a public API through its `index.ts`. Imports from outside a module must go through `index.ts` — never import internal files directly.

```typescript
// Correct
import { User } from '@/domain';

// Wrong
import { User } from '@/domain/entities/user.entity';
```

---

## Notes

<!-- Any project-specific structural decisions or deviations from the template -->

-
