# Stack: [PROJECT NAME]

> Technology stack, key dependencies, and tooling decisions.

---

## Language & Runtime

| Item | Version | Notes |
|------|---------|-------|
| Language | [e.g., TypeScript 5.x] | |
| Runtime | [e.g., Node.js 22 LTS] | |
| Package Manager | [e.g., pnpm 9.x] | |

---

## Frameworks & Libraries

### Core

| Library | Version | Purpose |
|---------|---------|---------|
| [e.g., Express] | [v] | HTTP server |
| [e.g., Prisma] | [v] | ORM |

### Testing

| Library | Version | Purpose |
|---------|---------|---------|
| [e.g., Vitest] | [v] | Unit & integration tests |
| [e.g., Supertest] | [v] | HTTP integration tests |

### Tooling

| Tool | Version | Purpose |
|------|---------|---------|
| [e.g., ESLint] | [v] | Linting |
| [e.g., Prettier] | [v] | Formatting |
| [e.g., tsx / ts-node] | [v] | Dev runner |

---

## Build & Scripts

```json
{
  "scripts": {
    "build": "[build command]",
    "start": "[start command]",
    "dev": "[dev command]",
    "test": "[test command]",
    "test:watch": "[watch command]",
    "lint": "[lint command]"
  }
}
```

---

## Infrastructure

| Component | Technology | Notes |
|-----------|------------|-------|
| Database | [e.g., PostgreSQL 16] | |
| Cache | [e.g., Redis 7] | |
| Message Broker | [e.g., RabbitMQ] | |
| Container | [e.g., Docker + Compose] | |

---

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `DATABASE_URL` | Yes | Database connection string |
| `PORT` | No | HTTP port (default: 3000) |

---

## Constraints & Notes

<!-- Known version constraints, EOL warnings, or upgrade plans -->

-
