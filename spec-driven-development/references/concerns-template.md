# Concerns: [PROJECT NAME]

> Technical debt, fragile areas, known risks, and items requiring attention.

---

## Severity Levels

| Level | Meaning |
|-------|---------|
| CRITICAL | Blocking issue — must be addressed before new features |
| HIGH | Significant risk — schedule for near-term resolution |
| MEDIUM | Manageable debt — address during next refactor |
| LOW | Minor issue — address opportunistically |

---

## Active Concerns

### [CONCERN-001] [Short title]

- **Severity**: [CRITICAL / HIGH / MEDIUM / LOW]
- **Area**: [e.g., Authentication, Database layer, CI pipeline]
- **Description**: [What the problem is and where it lives]
- **Impact**: [What breaks or degrades if this is ignored]
- **Root Cause**: [Why this exists — time pressure, legacy decision, missing knowledge]
- **Remediation**: [Proposed solution or workaround]
- **Effort**: [e.g., Small / Medium / Large]
- **Identified At**: [YYYY-MM-DD]

---

## Technical Debt Log

| ID | Area | Description | Severity | Identified | Status |
|----|------|-------------|----------|-----------|--------|
| CONCERN-001 | [area] | [brief description] | HIGH | [date] | Open |

---

## Fragile Areas

<!-- Code areas that are known to break easily or require special care when modified -->

| Area / File | Why It's Fragile | Precautions |
|-------------|-----------------|-------------|
| [e.g., `src/payments/retry.ts`] | Complex state machine, no tests | Add tests before modifying |
| [e.g., Database migrations] | Manual steps, no rollback | Test on staging first |

---

## Security Concerns

<!-- Known security issues or areas that need a security review -->

| Concern | Status | Notes |
|---------|--------|-------|
| [e.g., Secrets in logs] | Open | Check all log.info calls |
| [e.g., SQL injection in search] | Resolved | Migrated to Prisma ORM |

---

## Performance Concerns

<!-- Known bottlenecks or scalability risks -->

| Area | Concern | Measured? | Notes |
|------|---------|-----------|-------|
| [e.g., User list endpoint] | N+1 query | No | Needs eager loading |

---

## Resolved Concerns

<!-- Keep a record of addressed concerns for historical reference -->

| ID | Description | Resolved At | How |
|----|-------------|------------|-----|
| | | | |

---

## Notes

<!-- Anything that doesn't fit the above categories but warrants attention -->

-
