# Execution Plan: [FEATURE NAME]

> Batched execution plan derived from the dependency graph.

**Feature ID**: [FEAT-XXX]
**Generated At**: [YYYY-MM-DD]
**Status**: Pending Approval | Approved | In Progress | Completed

---

## Dependency Graph Summary

```yaml
# graph.yaml summary
tasks:
  T-001:
    depends_on: []
  T-002:
    depends_on: []
  T-003:
    depends_on:
      - T-001
      - T-002
```

---

## Execution Batches

### Batch 1 — Parallel *(can be executed simultaneously)*

| Task | Title | Estimate | Can Parallelize |
|------|-------|----------|-----------------|
| T-001 | [title] | [Xh] | Yes — independent |
| T-002 | [title] | [Xh] | Yes — independent |

**Orchestration note**: T-001 and T-002 have no dependencies. If sub-agents are available, execute in parallel.

---

### Batch 2 — Sequential *(requires Batch 1 completion)*

| Task | Title | Estimate | Depends On |
|------|-------|----------|------------|
| T-003 | [title] | [Xh] | T-001, T-002 |

---

## Estimates

| Metric | Value |
|--------|-------|
| Total tasks | [N] |
| Total estimated effort | [Xh] |
| Sequential minimum (critical path) | [Xh] |
| Parallel minimum (all parallelism exploited) | [Xh] |

---

## Execution Progress

| Task | Status | Started At | Completed At | Commit |
|------|--------|-----------|-------------|--------|
| T-001 | Pending | - | - | - |
| T-002 | Pending | - | - | - |
| T-003 | Pending | - | - | - |

**Status values**: `Pending` | `In Progress` | `Review Pending` | `Completed` | `Failed` | `Blocked`

---

## Approval Gate

```
Este é o plano de execução para [FEATURE NAME].

Batches:
  Batch 1 (paralelo): T-001, T-002
  Batch 2 (sequencial): T-003

Estimativa total: [Xh]
Estimativa com paralelismo: [Xh]

Você aprova iniciar a implementação?
```

---

## Retry Log

| Task | Attempt | Failed At | Reason | Next Attempt |
|------|---------|-----------|--------|-------------|
| - | - | - | - | - |

---

## Notes

<!-- Implementation notes, risks, or dependencies discovered during planning -->

-
