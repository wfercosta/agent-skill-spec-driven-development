# Script: generate-tasks

> Decompose a feature SDD into atomic, implementable tasks with explicit dependencies and parallelism opportunities.

---

## Objective

Produce a complete `tasks.md` and `graph.yaml` for a feature by breaking down the SDD into the smallest possible tasks that leave the application in a buildable state. Each coding task must follow the TDD pattern. Update STATE.md to `TASKS_GENERATED`.

---

## Inputs

- `.specs/features/[feature-name]/spec.md`
- `.specs/features/[feature-name]/design.md`
- `.specs/features/[feature-name]/context.md` (if exists)
- `.specs/codebase/ARCHITECTURE.md`
- `.specs/codebase/TESTING.md`
- `.specs/codebase/CONVENTIONS.md`

---

## Pre-conditions

1. `spec.md` and `design.md` must exist for the target feature.
2. Read all input documents before starting.
3. `STATE.md` must be at `DESIGN_DEFINED` or later.

---

## Steps

### Step 1 — Read and Internalize All Inputs

Read `spec.md`, `design.md`, `context.md`, and the codebase documents.

Extract:
- All acceptance criteria from `spec.md`.
- All components from `design.md`.
- Data model changes.
- API contracts.
- Error handling requirements.

### Step 2 — Identify Work Areas

Group work into logical areas based on the design:

1. **Infrastructure / Setup** — scaffolding, config, migrations, new modules.
2. **Domain** — entities, value objects, business rules.
3. **Application** — use cases, orchestration.
4. **Infrastructure Adapters** — repositories, external service clients, event publishers.
5. **Interface** — HTTP controllers, CLI commands, event consumers.
6. **Tests** — integration tests, e2e tests (unit tests are part of each coding task).
7. **Documentation** — updates to codebase docs if architecture changes.

### Step 3 — Decompose into Atomic Tasks

For each work area, create the smallest possible task that:
- Has a single, clear responsibility.
- Leaves the application in a **compilable and runnable state** when complete.
- Has measurable acceptance criteria.
- Can be reviewed in isolation.

**Decomposition rules:**
- One task per entity or value object.
- One task per use case.
- One task per repository interface + implementation.
- One task per HTTP endpoint or CLI command.
- One task per external integration adapter.
- One task for database migrations (separate from entity creation).
- One task for integration/e2e tests if they span multiple components.

**Task numbering:** `T-001`, `T-002`, ..., `T-NNN` (sequential within the feature).

### Step 4 — Apply TDD Pattern to Coding Tasks

For every task that involves writing, modifying, or deleting code:

Mark it as requiring TDD and structure the TDD steps:

```
TDD Steps:
1. Write tests in [test file path] covering:
   - [Test scenario 1: happy path]
   - [Test scenario 2: error case]
   - [Test scenario N: edge case]
2. Confirm tests fail.
3. Implement [specific code].
4. Confirm tests pass.
5. Run full test suite and build.
```

Test file paths must follow the project's testing conventions from `TESTING.md`.

### Step 5 — Identify Dependencies

For each task, determine which other tasks must be complete before it can start:

- A use case task depends on the domain entity task.
- An HTTP controller task depends on the use case task.
- A repository implementation task depends on the domain interface task.
- A migration task that is required for a use case to work creates a dependency.
- Tasks that are independent have `depends_on: []`.

### Step 6 — Identify Parallelism Opportunities

Mark task groups that can execute in parallel (tasks with no dependency between them):

```
# Example parallel groups:
Parallel Group A: T-001 (domain entity), T-002 (migration)
Parallel Group B: T-003 (use case), T-004 (repository impl) — after T-001 and T-002
```

Add a note to each independent task: `Can parallelize: Yes`.

### Step 7 — Write `tasks.md`

Using `references/task-template.md` as the base, write each task with:
- Complete description of what must be done.
- TDD steps (for coding tasks).
- Acceptance criteria traceable to requirements from `spec.md`.
- Dependencies listed explicitly.

Write to: `.specs/features/[feature-name]/tasks.md`

### Step 8 — Write `graph.yaml`

Generate a YAML dependency graph:

```yaml
tasks:
  T-001:
    title: "[title]"
    depends_on: []
    can_parallelize: true

  T-002:
    title: "[title]"
    depends_on: []
    can_parallelize: true

  T-003:
    title: "[title]"
    depends_on:
      - T-001
      - T-002
    can_parallelize: false
```

Write to: `.specs/features/[feature-name]/graph.yaml`

### Step 9 — Validate Task Set

Before writing the files, verify:

- [ ] Every acceptance criterion from `spec.md` is covered by at least one task.
- [ ] No task leaves the application in a broken/non-compilable state.
- [ ] All TDD tasks have concrete test file paths and test scenarios.
- [ ] Dependencies form a valid DAG (no circular dependencies).
- [ ] Tasks are small enough to be reviewed in a single session (max ~8h estimate per task).
- [ ] Every task is traceable to at least one requirement ID from `spec.md`.

### Step 10 — Update `STATE.md`

Call `scripts/update-state.md` with:
```
Status: TASKS_GENERATED
Feature: [feature-name]
Updated At: [current timestamp]
```

---

## Outputs

- `.specs/features/[feature-name]/tasks.md`
- `.specs/features/[feature-name]/graph.yaml`
- `.specs/STATE.md` updated to `TASKS_GENERATED`

---

## Completion Message

```
Tarefas geradas para "[feature-name]".

Total de tarefas: [N]
Tarefas paralelas identificadas: [N] (em [N] grupos)
Estimativa total: ~[Xh]

Documentos criados:
- .specs/features/[feature-name]/tasks.md
- .specs/features/[feature-name]/graph.yaml

Status atual: TASKS_GENERATED

Próximos passos:
- Revise as tarefas e ajuste estimativas ou dependências.
- Para gerar o plano de execução, execute: "generate execution plan for [feature-name]"
```

---

## Error Handling

- If `spec.md` or `design.md` are incomplete, do not proceed — inform the user of the missing information and ask them to complete the documents first.
- If a task would leave the application in a broken state, split it into a smaller task that maintains compilability.
- If circular dependencies are detected in the graph, resolve them by splitting or reordering tasks and flag the issue to the user.
