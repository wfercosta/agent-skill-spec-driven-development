# Script: retry-task

> Retry a failed task with exponential backoff. Mark the task as BLOCKED after exceeding max attempts.

---

## Objective

Handle task failures gracefully by retrying up to 3 times with exponential backoff. If all attempts are exhausted, update STATE.md to `BLOCKED` and notify the user with a detailed failure report.

---

## Inputs

- Task ID (e.g., `T-003`).
- Failure description (what failed, error messages, last attempt details).
- `.specs/features/[feature-name]/tasks.md`
- `.specs/features/[feature-name]/execution.md`
- `.specs/STATE.md`

---

## Retry Policy

```yaml
retry_policy:
  max_attempts: 3
  backoff:
    attempt_1: immediate   # retry right away
    attempt_2: 30 seconds  # wait before second retry
    attempt_3: 120 seconds # wait before third retry
  on_exhaustion: BLOCKED
```

---

## Steps

### Step 1 — Read Retry History

Read `execution.md` and find the retry log for the target task.

Determine `current_attempt_count`:
- If no retry log entry exists: `current_attempt_count = 0`.
- Otherwise: read the count from the retry log.

### Step 2 — Check Attempt Count

```
If current_attempt_count >= 3:
    → Go to Step 7 (Mark as BLOCKED)
Else:
    → Continue to Step 3
```

### Step 3 — Log the Failure

Append to the retry log in `execution.md`:

```markdown
| [task ID] | [attempt N] | [timestamp] | [failure reason] | [next attempt timestamp] |
```

### Step 4 — Diagnose the Failure

Before retrying, analyze the failure:

1. Read the error messages and stack traces from the previous attempt.
2. Determine the failure category:
   - **Transient failure** (e.g., network timeout, flaky test, race condition): retry with same approach.
   - **Logic failure** (e.g., wrong implementation, misunderstood requirement): adjust the approach before retrying.
   - **Environment failure** (e.g., missing dependency, broken tool): resolve the environment issue first.
   - **Specification gap** (e.g., requirement is ambiguous, design is incomplete): do not retry — escalate to the user.

3. If the failure is a Specification Gap, go directly to Step 6 (escalate).

### Step 5 — Retry with Backoff

Apply the backoff delay appropriate for the current attempt:

| Attempt | Backoff |
|---------|---------|
| 1st retry | Immediate |
| 2nd retry | 30 seconds |
| 3rd retry | 120 seconds |

Increment `current_attempt_count` by 1.

Update `execution.md` retry log with the new attempt start time.

Re-execute the task using `scripts/execute-task.md` with any adjustments identified in Step 4.

If the retry succeeds: mark the task as `Completed` in `execution.md` and continue normally.

If the retry fails: return to Step 2 with the incremented count.

### Step 6 — Escalate Specification Gap

If the failure is due to an ambiguous specification or incomplete design:

```
A tarefa [T-XXX] falhou devido a uma lacuna na especificação:

Problema identificado: [description]

Ação necessária:
- Revisar .specs/features/[feature-name]/spec.md — [specific section]
- OU revisar .specs/features/[feature-name]/design.md — [specific section]

O que gostaria de fazer?
1. Atualizar a especificação e re-gerar as tarefas afetadas.
2. Fornecer esclarecimentos diretamente para que eu possa prosseguir.
3. Pular esta tarefa e continuar com as próximas (marcar como bloqueada).
```

Wait for user response before taking any action.

### Step 7 — Mark as BLOCKED (exhausted retries)

After 3 failed attempts:

**Update `execution.md`:**
Mark the task as `Blocked`.

```markdown
| T-XXX | Blocked | [timestamp] | [final failure reason] | - |
```

**Call `scripts/update-state.md`:**
```
Status: BLOCKED
Feature: [feature-name]
Current Task: T-XXX
blocked_task: T-XXX
blocked_reason: [failure reason summary]
```

**Notify the user:**

```
A tarefa [T-XXX]: "[task title]" foi bloqueada após 3 tentativas.

Histórico de falhas:
  Tentativa 1: [timestamp] — [reason]
  Tentativa 2: [timestamp] — [reason]
  Tentativa 3: [timestamp] — [reason]

Causa provável: [diagnosis]

Tarefas bloqueadas como consequência (dependem de T-XXX):
  - [T-YYY]: "[title]"
  - [T-ZZZ]: "[title]"

Opções:
1. Fornecer mais contexto ou uma abordagem alternativa para que eu tente novamente.
2. Resolver o bloqueio manualmente e me informar quando estiver pronto.
3. Remover a dependência em T-XXX e continuar com outras tarefas.
4. Revisar a especificação ou design desta tarefa.
```

Wait for user direction.

---

## Outputs

- Updated `execution.md` retry log.
- Updated `STATE.md` (either back to `TASK_IN_PROGRESS` on success, or `BLOCKED` on exhaustion).
- User notification with failure report and options.

---

## Error Handling

- If `execution.md` or `tasks.md` cannot be read, report the issue and halt.
- If the backoff timer is not applicable in the current context (interactive session), simply note the recommended wait time and proceed when the user confirms.
- Never silently swallow retry failures — always update the retry log and notify the user.
