# Script: execute-task

> Execute a single task following TDD, present the implementation for human review, and perform a controlled commit upon approval.

---

## Objective

Implement one task from the approved execution plan. Follow TDD strictly. After implementation, present the result to the human for review. Upon approval, commit the changes with a conventional commit message. Ask whether to continue to the next task.

---

## Inputs

- Task ID (e.g., `T-001`).
- `.specs/features/[feature-name]/tasks.md` — task definition.
- `.specs/features/[feature-name]/execution.md` — execution plan and progress tracker.
- `.specs/features/[feature-name]/spec.md` — requirements and acceptance criteria.
- `.specs/features/[feature-name]/design.md` — design decisions.
- `.specs/codebase/CONVENTIONS.md` — naming and coding conventions.
- `.specs/codebase/TESTING.md` — test patterns.

---

## Pre-conditions

1. The execution plan must have been approved (STATE is `TASK_IN_PROGRESS` or approved by the user).
2. All tasks listed as dependencies for the target task must be `Completed` in `execution.md`.
3. The build must be clean before starting (run build and tests to confirm baseline).

---

## Steps

### Step 1 — Read Task Definition

Read the target task from `tasks.md`. Extract:
- Description.
- TDD steps and test scenarios.
- Acceptance criteria.
- Dependencies (verify all are completed).

### Step 2 — Update STATE to `TASK_IN_PROGRESS`

Call `scripts/update-state.md`:
```
Status: TASK_IN_PROGRESS
Feature: [feature-name]
Current Task: [T-XXX]
Updated At: [current timestamp]
```

Update `execution.md` — mark the task as `In Progress` with start timestamp.

### Step 3 — TDD: Write Tests First (if coding task)

Before writing any implementation code:

1. Identify or create the test file at the path specified in the task definition.
2. Write all test cases specified in the task's TDD steps:
   - Happy path test(s).
   - Error/failure test(s).
   - Edge case test(s).
3. Run the tests.
4. **Confirm they FAIL** — if any test passes before implementation, the test is wrong. Fix the test before proceeding.

If this is NOT a coding task (e.g., documentation, migration script only, config), skip to Step 5.

### Step 4 — Implement the Code

With failing tests in place, write the minimum production code required to make the tests pass:

- Follow the design from `design.md`.
- Follow naming conventions from `CONVENTIONS.md`.
- Do not write more code than needed to satisfy the tests.
- Do not implement behavior from other tasks.

After implementation:
1. Run the tests for this task — **all must pass**.
2. Run the full test suite — **no regressions allowed**.
3. Run the build — **must compile without errors**.

If any test fails or the build breaks, fix the issue before proceeding. Do NOT skip or comment out failing tests.

### Step 5 — Verify Acceptance Criteria

Read the acceptance criteria from the task definition. For each criterion:
- Verify it is satisfied by the implementation.
- If a criterion is not met, continue implementation until it is.
- Check if any requirement from `spec.md` that this task traces to is now satisfied.

### Step 6 — Update `execution.md`

Mark the task as `Review Pending` in the progress tracker.

### Step 7 — Update `STATE.md` to `IMPLEMENTATION_REVIEW_PENDING`

Call `scripts/update-state.md`:
```
Status: IMPLEMENTATION_REVIEW_PENDING
Feature: [feature-name]
Current Task: [T-XXX]
Updated At: [current timestamp]
```

### Step 8 — Present Implementation for Review (MANDATORY GATE)

Present a summary of all changes made and request human review:

```
A tarefa [T-XXX]: "[task title]" foi implementada.

Arquivos criados/modificados:
  + [new file path]
  ~ [modified file path]
  - [deleted file path]

Resultados dos testes:
  ✓ [N] testes passando
  ✗ 0 falhas

Build: OK

Critérios de aceitação:
  [X] [Criterion 1]
  [X] [Criterion 2]

Revise as mudanças e me informe se aprova o commit.
```

Wait for explicit approval before committing.

### Step 9 — Handle Review Response

**If approved:** proceed to Step 10.

**If changes requested:**
- Apply the requested changes.
- Re-run tests and build.
- Return to Step 8 with the updated summary.

**If rejected entirely:**
- Ask the user what should be done differently.
- Revert or rework as instructed.
- Return to Step 3 or Step 4 as appropriate.

### Step 10 — Update STATE to `COMMIT_APPROVAL_PENDING`

Call `scripts/update-state.md`:
```
Status: COMMIT_APPROVAL_PENDING
Feature: [feature-name]
Current Task: [T-XXX]
Updated At: [current timestamp]
```

### Step 11 — Generate Commit Message

Generate a commit message following the Conventional Commits specification:

```
<type>(<scope>): <short summary>

<optional body: what was done and why>

Refs: [FEAT-XXX-REQ-NNN], T-XXX
```

Types: `feat`, `fix`, `test`, `refactor`, `docs`, `chore`, `perf`

Present to the user:
```
Mensagem de commit proposta:

[commit message]

Confirma o commit?
```

### Step 12 — Perform Commit

Upon confirmation:
1. Stage all relevant files (`git add`).
2. Commit with the approved message.
3. Confirm the commit hash to the user.

### Step 13 — Update `execution.md` and `tasks.md`

- Mark the task as `Completed` with timestamp and commit hash in `execution.md`.
- Mark the task checkbox as complete in `tasks.md`.

### Step 14 — Update `STATE.md`

Check if all tasks in the feature are completed:
- **If more tasks remain**: set state back to `TASK_IN_PROGRESS`.
- **If all tasks completed**: set state to `FEATURE_COMPLETED`.

Call `scripts/update-state.md` accordingly.

### Step 15 — Ask to Continue

```
[T-XXX] concluída e commitada.

[If next task exists:]
Próxima tarefa: [T-YYY] — "[next task title]"
Dependências satisfeitas: Sim

Deseja continuar com [T-YYY]?

[If all tasks completed:]
Todas as tarefas da feature "[feature-name]" foram concluídas.
Status: FEATURE_COMPLETED
```

---

## Outputs

- Implemented code in the project.
- Updated `execution.md` (task marked Completed).
- Updated `tasks.md` (task checkbox marked).
- Updated `STATE.md`.
- A git commit with conventional commit message.

---

## TDD Invariants

The following rules must NEVER be violated:

1. Tests must be written BEFORE implementation code.
2. Tests must FAIL before implementation (red phase).
3. Tests must PASS after implementation (green phase).
4. The build must be clean after each task.
5. No test may be skipped, commented out, or marked as todo to make the suite pass.

---

## Error Handling

- Build fails after implementation: fix the compilation error before presenting for review. Never present broken code.
- Tests fail after implementation: debug and fix. Never leave failing tests.
- Implementation breaks unrelated tests (regression): fix the regression before proceeding, even if it requires reworking the task approach.
- User approval is not given after 3 review cycles: escalate to the user with a summary of the sticking points and ask how to proceed.
