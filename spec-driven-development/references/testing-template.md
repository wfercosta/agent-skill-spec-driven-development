# Testing: [PROJECT NAME]

> Test strategy, frameworks, patterns, and coverage expectations.

---

## Strategy Overview

| Level | Scope | Tools | Goal |
|-------|-------|-------|------|
| Unit | Single function/class, no I/O | [e.g., Vitest] | Business logic correctness |
| Integration | Module + real dependencies | [e.g., Vitest + Testcontainers] | Adapter and DB correctness |
| E2E | Full system via public interface | [e.g., Supertest / Playwright] | User-facing behavior |

---

## Test Frameworks & Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| [e.g., Vitest] | [v] | Test runner and assertions |
| [e.g., @vitest/coverage-v8] | [v] | Coverage reporting |
| [e.g., Supertest] | [v] | HTTP integration testing |
| [e.g., testcontainers] | [v] | Real DB/services in tests |
| [e.g., @faker-js/faker] | [v] | Test data generation |

---

## Running Tests

```bash
# All tests
[test command]

# Watch mode
[watch command]

# Coverage
[coverage command]

# Specific file
[runner] [path/to/file.test.ts]

# Specific pattern
[runner] --grep "UserService"
```

---

## File Organization

```
# Option A: Co-located (next to source)
src/
└── users/
    ├── user.service.ts
    └── user.service.test.ts

# Option B: Parallel directory
test/
└── unit/
    └── users/
        └── user.service.test.ts
```

> This project uses: **[Option A / Option B]**

---

## Test Structure

```typescript
describe('[ClassName or function name]', () => {
  // Shared setup
  let sut: UserService;
  let mockRepository: MockUserRepository;

  beforeEach(() => {
    mockRepository = new MockUserRepository();
    sut = new UserService(mockRepository);
  });

  describe('[method name]', () => {
    it('should [expected behavior] when [condition]', async () => {
      // Arrange
      const input = { ... };

      // Act
      const result = await sut.method(input);

      // Assert
      expect(result).toEqual({ ... });
    });

    it('should throw [ErrorType] when [invalid condition]', async () => {
      // Arrange
      const invalid = { ... };

      // Act & Assert
      await expect(sut.method(invalid)).rejects.toThrow(SomeError);
    });
  });
});
```

---

## Mocking

- Prefer manual mocks over auto-mocking for clarity.
- Mock only at system boundaries (repositories, external APIs, queues).
- Do not mock domain logic — test it directly.
- Reset all mocks in `beforeEach` or `afterEach`.

```typescript
// Example manual mock
class MockUserRepository implements UserRepository {
  users: User[] = [];

  async findById(id: string): Promise<User | null> {
    return this.users.find(u => u.id === id) ?? null;
  }
}
```

---

## Coverage Requirements

| Metric | Minimum |
|--------|---------|
| Statements | [e.g., 80%] |
| Branches | [e.g., 75%] |
| Functions | [e.g., 80%] |
| Lines | [e.g., 80%] |

---

## TDD Workflow (Mandatory for coding tasks)

1. Write failing test(s) that describe the expected behavior.
2. Run tests — confirm they fail for the right reason.
3. Write the minimum code to make the tests pass.
4. Run tests — confirm they pass.
5. Refactor if needed — tests must still pass.
6. Run the full test suite before committing.

---

## Test Data

- Use factory functions or builders to create test data.
- Never use hardcoded IDs that could conflict between tests.
- For integration tests, use isolated database schemas or transactions that roll back.

---

## Notes

<!-- Project-specific testing decisions, known flaky tests, or areas with low coverage -->

-
