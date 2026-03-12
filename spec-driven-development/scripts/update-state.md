# Script: update-state

> Update `.specs/STATE.md` with the current workflow state and optional metadata.

---

## Objective

Maintain an accurate, timestamped record of the current development state in `.specs/STATE.md`. This script is called by other scripts — it should not be called directly by the user unless they need to manually correct the state.

---

## Inputs

| Parameter | Required | Description |
|-----------|----------|-------------|
| `status` | Yes | New state value (see valid states below) |
| `feature` | No | Feature name currently being worked on |
| `current_task` | No | Task ID currently in progress |
| `note` | No | A brief note to add to the status history |
| `blocked_task` | No | Task ID to mark as blocked (used with `BLOCKED` status) |
| `blocked_reason` | No | Reason for the block |

---

## Valid States

```
UNINITIALIZED
CODEBASE_ANALYZED
FEATURE_SPECIFIED
DESIGN_DEFINED
TASKS_GENERATED
EXECUTION_PLAN_READY
PLAN_APPROVAL_PENDING
TASK_IN_PROGRESS
IMPLEMENTATION_REVIEW_PENDING
COMMIT_APPROVAL_PENDING
FEATURE_COMPLETED
BLOCKED
```

---

## Steps

### Step 1 — Read Current STATE.md

Read `.specs/STATE.md`. If the file does not exist, create it using `references/state-template.md` as the base.

### Step 2 — Validate Transition

Verify the new status is a valid state from the list above. If not, log an error and do not update the file.

Optional: warn if the transition skips expected intermediate states (e.g., jumping from `UNINITIALIZED` directly to `TASK_IN_PROGRESS`), but do not block the update — the user may have a valid reason.

### Step 3 — Update Header Fields

Update the top-level fields:

```markdown
**Status**: [new status]
**Feature**: [feature name or current value if not provided]
**Current Task**: [task ID or '-' if not applicable]
**Updated At**: [YYYY-MM-DD HH:MM]
```

### Step 4 — Append to Status History

Add a new row to the Status History table:

```markdown
| [YYYY-MM-DD HH:MM] | [new status] | [note if provided, otherwise '-'] |
```

### Step 5 — Handle BLOCKED State

If the new status is `BLOCKED`:

1. Add or update the Blockers section:
   ```markdown
   ## Blockers

   BLOCKED_TASK: [task ID]
   BLOCKED_REASON: [reason]
   BLOCKED_AT: [YYYY-MM-DD HH:MM]
   ```

### Step 6 — Handle FEATURE_COMPLETED State

If the new status is `FEATURE_COMPLETED`:

1. Clear the `Current Task` field: `-`.
2. Add a note to the status history: `Feature [feature-name] completed`.
3. Optionally add a learning to the Learnings section if a notable insight was gained during implementation.

### Step 7 — Write Updated STATE.md

Write the updated content back to `.specs/STATE.md`.

---

## Outputs

- `.specs/STATE.md` updated with new status, timestamp, and history entry.

---

## Example STATE.md After Update

```markdown
# Project State

**Status**: TASK_IN_PROGRESS
**Feature**: payment-retry
**Current Task**: T-002
**Updated At**: 2026-03-12 14:30

---

## Status History

| Timestamp | Status | Notes |
|-----------|--------|-------|
| 2026-03-10 09:00 | UNINITIALIZED | Project initialized |
| 2026-03-10 09:05 | CODEBASE_ANALYZED | Brownfield analysis complete |
| 2026-03-11 10:00 | DESIGN_DEFINED | SDD for payment-retry approved |
| 2026-03-11 15:00 | TASKS_GENERATED | 5 tasks generated |
| 2026-03-12 09:00 | EXECUTION_PLAN_READY | Plan presented for approval |
| 2026-03-12 09:10 | TASK_IN_PROGRESS | T-001 completed, starting T-002 |

---

## Current Feature

payment-retry

...
```

---

## Error Handling

- If `STATE.md` cannot be written (permission error), report immediately and halt the calling script.
- If the status value is not in the valid states list, do not update and report the invalid value.
- Never silently swallow update failures — always report them.
