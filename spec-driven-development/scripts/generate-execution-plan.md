# Script: generate-execution-plan

> Build a batched execution plan from the task dependency graph and request human approval before implementation begins.

---

## Objective

Read `graph.yaml`, perform a topological sort, group tasks into executable batches that maximize parallelism, produce `execution.md`, update STATE.md, and present the plan to the user for approval. Do not begin any implementation until approval is received.

---

## Inputs

- `.specs/features/[feature-name]/tasks.md`
- `.specs/features/[feature-name]/graph.yaml`

---

## Pre-conditions

1. `tasks.md` and `graph.yaml` must exist.
2. `STATE.md` must be at `TASKS_GENERATED` or later.
3. The dependency graph must be a valid DAG (no cycles).

---

## Steps

### Step 1 — Read Inputs

Read `graph.yaml` and `tasks.md`.

Extract for each task:
- Task ID, title, estimate.
- `depends_on` list.
- `can_parallelize` flag.

### Step 2 — Validate Dependency Graph

Check for:
- **Cycles**: if task A depends on B and B depends on A (directly or transitively), abort and report the cycle to the user.
- **Missing references**: if a task lists a dependency that doesn't exist in the graph, report it.
- **Self-dependencies**: a task cannot depend on itself.

If any validation fails, do not proceed. Report the issue and wait for the user to fix `graph.yaml` or `tasks.md`.

### Step 3 — Topological Sort

Perform a topological sort (Kahn's algorithm or DFS-based) on the dependency graph to produce a valid execution order.

### Step 4 — Group into Batches

A batch is a set of tasks that can all start at the same time because their dependencies are satisfied by previous batches.

Algorithm:
1. Batch 1 = all tasks with `depends_on: []`.
2. Batch N = all tasks whose dependencies are all in Batches 1..N-1.
3. Repeat until all tasks are assigned.

Within each batch, identify tasks that can be executed in parallel (tasks with `can_parallelize: true` and no dependency between them within the same batch).

### Step 5 — Calculate Estimates

For each batch:
- **Sequential estimate**: sum of all task estimates.
- **Parallel estimate**: estimate of the longest task in the batch (critical path).

Total estimates:
- **Total effort (sequential)**: sum of all task estimates.
- **Minimum wall time (with full parallelism)**: sum of the critical path across batches.

### Step 6 — Write `execution.md`

Using `references/execution-template.md` as the base, populate:
- Dependency graph summary (reproduced from graph.yaml for quick reference).
- Each batch with its tasks and parallelism notes.
- Estimates table.
- Progress tracker (all tasks start as `Pending`).
- The approval gate message.

Write to: `.specs/features/[feature-name]/execution.md`

### Step 7 — Update `STATE.md` to `EXECUTION_PLAN_READY`

Call `scripts/update-state.md` with:
```
Status: EXECUTION_PLAN_READY
Feature: [feature-name]
Updated At: [current timestamp]
```

### Step 8 — Present Plan and Request Approval (MANDATORY GATE)

Present the plan to the user and wait for explicit approval. Do NOT begin any implementation without approval.

```
Plano de execução para "[feature-name]":

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Batch 1 — Paralelo (podem ser executadas simultaneamente):
  • T-001: [title] (~[Xh])
  • T-002: [title] (~[Xh])

Batch 2 — Sequencial (requer conclusão do Batch 1):
  • T-003: [title] (~[Xh]) — depende de T-001, T-002

[... continue for all batches ...]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Estimativa total de esforço: ~[Xh]
Estimativa com paralelismo:  ~[Xh]
Total de tarefas:            [N]

Você aprova iniciar a implementação?
```

### Step 9 — Handle Approval Response

**If approved:**
- Update `STATE.md` to `PLAN_APPROVAL_PENDING` momentarily, then immediately to `TASK_IN_PROGRESS`.
- Confirm:
  ```
  Plano aprovado. Iniciando implementação com o Batch 1.
  ```
- Begin with the first batch. If the batch has parallel tasks and sub-agents are available, act as Orchestrator and spawn sub-agents.

**If not approved:**
- Ask what changes the user wants:
  ```
  Entendido. O que você gostaria de ajustar no plano?
  (Ex.: reorganizar tarefas, ajustar estimativas, dividir ou mesclar tarefas)
  ```
- Apply requested changes, update `tasks.md`, `graph.yaml`, and `execution.md`, then re-present for approval.

**If user requests changes to scope:**
- Go back to `generate-sdd.md` or `generate-tasks.md` as appropriate.

---

## Outputs

- `.specs/features/[feature-name]/execution.md`
- `.specs/STATE.md` updated (to `EXECUTION_PLAN_READY`, then to `TASK_IN_PROGRESS` upon approval)

---

## Error Handling

- Cycle in dependency graph: abort, report the cycle with the task IDs involved, wait for correction.
- Missing tasks referenced as dependencies: abort, report the missing IDs, wait for correction.
- User does not approve and provides no feedback: ask again with a prompt for specific concerns.
- Execution plan becomes invalid after scope changes: regenerate tasks and graph before re-running this script.
