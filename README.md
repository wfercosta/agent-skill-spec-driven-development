# Spec-Driven Development Skill

A Claude Code skill that transforms an AI agent into a structured software engineering assistant. It enforces a disciplined workflow where **specifications always precede implementation**, humans approve key steps, and all work is traceable through a persistent state machine.

## Principles

1. **Spec First** — No implementation occurs before a specification exists.
2. **Human in the Loop** — The developer must approve: the execution plan, each implementation, and each commit.
3. **Small Cognitive Steps** — Work is broken into small, reviewable atomic tasks.
4. **Deterministic Execution** — The agent follows an explicit and traceable plan.
5. **Continuous Documentation** — Documentation evolves alongside the system.

---

## How It Works

The skill manages a `.specs/` directory at the root of your project. This directory holds all specifications, designs, tasks, and state. The agent reads and writes to this directory as it progresses through the workflow.

### Workflow Overview

```
init project
    └─> CODEBASE_ANALYZED
            └─> create SDD for [feature]
                    └─> DESIGN_DEFINED
                            └─> generate tasks for [feature]
                                    └─> TASKS_GENERATED
                                            └─> generate execution plan for [feature]
                                                    └─> PLAN_APPROVAL_PENDING  ← human gate
                                                            └─> execute task [T-XXX]
                                                                    └─> IMPLEMENTATION_REVIEW_PENDING  ← human gate
                                                                            └─> COMMIT_APPROVAL_PENDING  ← human gate
                                                                                    └─> next task / FEATURE_COMPLETED
```

---

## Project Structure

The skill operates on this directory structure inside your project:

```
.specs/
├── STATE.md                    # State machine + decisions + blockers + learnings
│
├── codebase/                   # Generated during initialization
│   ├── STACK.md                # Technology stack and dependencies
│   ├── ARCHITECTURE.md         # Patterns, data flow, code organization
│   ├── CONVENTIONS.md          # Naming, style, coding patterns
│   ├── STRUCTURE.md            # Directory layout and modules
│   ├── TESTING.md              # Test frameworks and patterns
│   ├── INTEGRATIONS.md         # External services and APIs
│   └── CONCERNS.md             # Tech debt, risks, fragile areas
│
├── features/                   # One directory per feature
│   └── [feature-name]/
│       ├── spec.md             # Requirements with traceable IDs
│       ├── context.md          # Decisions for gray areas (when needed)
│       ├── design.md           # Architecture, components, Mermaid diagrams
│       ├── tasks.md            # Atomic tasks with dependencies
│       ├── execution.md        # Execution plan organized in batches
│       └── graph.yaml          # Dependency graph for parallelism detection
│
└── quick/                      # Small ad-hoc tasks
    └── NNN-slug/
        ├── TASK.md             # Description + verification criteria
        └── SUMMARY.md          # What was done + commit reference
```

---

## Installation

Copy the skill files into your Claude Code skills directory:

```bash
# Clone this repository
git clone https://github.com/your-org/agent-skill-spec-driven-development

# Copy to your Claude Code skills directory
cp -r agent-skill-spec-driven-development ~/.claude/skills/spec-driven-development
```

---

## Usage

### 1. Initialize a Project

```
init project
```

The agent will ask whether you want to initialize from an **existing codebase (brownfield)** or a **new project (greenfield)**, then generate all documents under `.specs/codebase/`.

### 2. Create a Software Design Document (SDD)

```
create SDD for payment retry
generate SDD from requirements.md
```

The agent asks clarifying questions to eliminate ambiguity, then generates `spec.md`, `design.md`, and optionally `context.md`.

### 3. Generate Atomic Tasks

```
generate tasks for feature payment-retry
generate tasks for SDD payment-retry
```

Produces `tasks.md` and `graph.yaml` with atomic tasks, dependencies, and parallelism opportunities.

### 4. Generate Execution Plan

```
generate execution plan for payment-retry
```

Builds a batched execution plan and waits for your approval before any implementation begins.

### 5. Execute a Task

```
execute task T-001
start implementation
```

Runs one task at a time using TDD. After each task, the agent presents the changes and waits for your review and commit approval.

### 6. Quick Tasks (no full SDD cycle)

```
quick task: fix error in login endpoint
quick fix: null pointer in payment service
fix bug in payment retry logic
```

Creates `.specs/quick/NNN-slug/TASK.md`, implements directly, and writes a `SUMMARY.md` when done.

---

## Human Gates

The agent **never** advances past these gates without explicit human approval.

| Gate | Trigger | Agent asks |
|------|---------|------------|
| **Gate 1 — Plan Approval** | Before any implementation | "Do you approve starting the implementation?" |
| **Gate 2 — Implementation Review** | After each task is complete | "Review the changes before approving the commit." |
| **Gate 3 — Commit Approval** | Before committing | "Confirm the commit?" |

---

## TDD Requirement

All coding tasks follow Test-Driven Development:

1. Write or update tests (unit, component, integration) — tests **must fail**.
2. Implement the minimum code to make tests pass.
3. Run the build — must succeed with no compilation errors.
4. Run all tests — must pass.

The agent never writes implementation code before the corresponding tests exist and fail.

---

## Parallelism

When the dependency graph (`graph.yaml`) reveals independent tasks within the same batch, the agent automatically switches from **Executor** to **Orchestrator** mode, spawning sub-agents for each independent task and consolidating results before moving to the next batch.

---

## Retry Policy

If a task fails during execution:

```yaml
retry_policy:
  max_attempts: 3
  backoff: exponential  # 1st: immediate, 2nd: 30s, 3rd: 120s
```

After 3 failures, `STATE.md` is updated to `BLOCKED` with the reason documented.

---

## Scripts Reference

| Script | Purpose |
|--------|---------|
| `scripts/init-brownfield.md` | Analyze existing codebase and generate `.specs/codebase/` |
| `scripts/init-greenfield.md` | Initialize new project and generate `.specs/codebase/` |
| `scripts/generate-sdd.md` | Generate SDD through iterative refinement with the user |
| `scripts/generate-design.md` | Generate design document with Mermaid diagrams |
| `scripts/generate-tasks.md` | Generate atomic tasks from SDD with dependency graph |
| `scripts/generate-execution-plan.md` | Build batched execution plan and request approval |
| `scripts/execute-task.md` | Execute a single task using TDD with human gates |
| `scripts/update-state.md` | Update `.specs/STATE.md` |
| `scripts/retry-task.md` | Retry a failed task with backoff |

## Templates Reference

| Template | Used For |
|----------|---------|
| `references/state-template.md` | `.specs/STATE.md` |
| `references/stack-template.md` | `.specs/codebase/STACK.md` |
| `references/architecture-template.md` | `.specs/codebase/ARCHITECTURE.md` |
| `references/conventions-template.md` | `.specs/codebase/CONVENTIONS.md` |
| `references/structure-template.md` | `.specs/codebase/STRUCTURE.md` |
| `references/testing-template.md` | `.specs/codebase/TESTING.md` |
| `references/integrations-template.md` | `.specs/codebase/INTEGRATIONS.md` |
| `references/concerns-template.md` | `.specs/codebase/CONCERNS.md` |
| `references/sdd-template.md` | `.specs/features/[name]/spec.md` |
| `references/design-template.md` | `.specs/features/[name]/design.md` |
| `references/task-template.md` | `.specs/features/[name]/tasks.md` |
| `references/execution-template.md` | `.specs/features/[name]/execution.md` |

---

## Governance Rules

The agent **MUST**:
- Keep `spec.md`, `design.md`, and `tasks.md` consistent with each other.
- Maintain requirements traceability: every task must reference at least one requirement ID from `spec.md`.
- Update `STATE.md` after each significant state change.
- Never skip a human gate under any circumstance.

The agent **MUST NEVER**:
- Implement without an approved spec.
- Start implementation without plan approval.
- Commit without human approval.
- Advance tasks automatically without human confirmation.
- Break the build or leave the application non-compilable at the end of any task.
