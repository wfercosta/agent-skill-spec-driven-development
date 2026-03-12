# Conventions: [PROJECT NAME]

> Naming, coding style, patterns, and team agreements that must be consistently applied.

---

## Naming Conventions

### Files & Directories

| Artifact | Convention | Example |
|----------|------------|---------|
| Source files | kebab-case | `user-repository.ts` |
| Test files | `[name].test.ts` | `user-repository.test.ts` |
| Directories | kebab-case | `use-cases/` |
| Classes | PascalCase | `UserRepository` |
| Interfaces | PascalCase (no `I` prefix) | `UserRepository` |
| Types | PascalCase | `CreateUserInput` |

### Code

| Artifact | Convention | Example |
|----------|------------|---------|
| Variables & functions | camelCase | `getUserById` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_ATTEMPTS` |
| Enums | PascalCase members | `Status.ACTIVE` |
| Private members | camelCase (no underscore) | `this.repository` |
| Boolean variables | `is/has/can` prefix | `isActive`, `hasPermission` |

---

## Import Order

```typescript
// 1. Node built-ins
import { readFile } from 'node:fs/promises';

// 2. External packages
import { z } from 'zod';

// 3. Internal — absolute paths (if configured)
import { UserRepository } from '@/domain/user';

// 4. Internal — relative paths
import { CreateUserInput } from './types';
```

---

## Error Handling

<!-- Describe the strategy: exceptions, Result type, Either, etc. -->

```typescript
// Example with Result pattern:
type Result<T> = { ok: true; value: T } | { ok: false; error: AppError };
```

---

## Commit Convention

Following [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <short summary>

Types: feat, fix, docs, style, refactor, test, chore, perf, ci
```

Examples:
```
feat(auth): add JWT refresh token support
fix(users): handle duplicate email on registration
test(payments): add integration tests for retry logic
```

---

## Code Style

| Rule | Value |
|------|-------|
| Indentation | 2 spaces |
| Quotes | Single (`'`) |
| Semicolons | Yes |
| Max line length | 100 characters |
| Trailing commas | All (ES5+) |

---

## Test Conventions

- Test files live next to source files OR in a parallel `test/` directory (pick one, be consistent).
- Each test file covers exactly one module.
- Describe blocks: `describe('[ClassName/FunctionName]', () => {})`.
- Test names: `it('should [expected behavior] when [condition]', ...)`.
- Avoid `test()` — prefer `it()` for readability.
- Use `beforeEach` to reset state, never share mutable state between tests.

---

## Documentation

- Public functions and classes must have JSDoc.
- Complex algorithms must have inline comments explaining *why*, not *what*.
- TODOs must reference a task ID: `// TODO(T-XXX): ...`.

---

## Patterns to Avoid

<!-- Anti-patterns specific to this project -->

- Do not use `any` in TypeScript — use `unknown` and narrow explicitly.
- Do not swallow errors silently — always log or propagate.
- Do not access environment variables outside of the configuration module.
