# Spec: [FEATURE NAME]

> Software Design Document — single source of truth for this feature.

**Feature ID**: [FEAT-XXX]
**Status**: Draft | Under Review | Approved
**Author**: [name or agent]
**Created At**: [YYYY-MM-DD]
**Last Updated**: [YYYY-MM-DD]

---

## 1. Overview

### Problem Statement

[What problem does this feature solve? Why does it need to exist?]

### Solution Summary

[High-level description of what will be built. One paragraph.]

### Scope

**In scope:**
- [Item 1]
- [Item 2]

**Out of scope:**
- [Item 1]
- [Item 2]

---

## 2. Stakeholders

| Role | Name / Team | Interest |
|------|-------------|----------|
| [e.g., Product Owner] | [name] | Feature approval |
| [e.g., Backend Team] | [name] | Implementation |
| [e.g., QA] | [name] | Test coverage |

---

## 3. Functional Requirements

### [FEAT-XXX-REQ-001] [Requirement title]

**Description**: [What the system must do]
**Priority**: Must Have | Should Have | Nice to Have
**Rationale**: [Why this requirement exists]

---

### [FEAT-XXX-REQ-002] [Requirement title]

**Description**: [What the system must do]
**Priority**: Must Have | Should Have | Nice to Have
**Rationale**: [Why this requirement exists]

---

## 4. Non-Functional Requirements

### Performance

- [e.g., Response time P95 must be under 200ms]
- [e.g., Must support 500 concurrent requests]

### Security

- [e.g., All endpoints must require authentication]
- [e.g., PII must be encrypted at rest]

### Availability & Reliability

- [e.g., SLA: 99.9% uptime]
- [e.g., Must recover from transient failures within 30 seconds]

### Scalability

- [e.g., Must scale horizontally without shared state]

### Observability

- [e.g., All operations must emit structured logs]
- [e.g., Key metrics must be exported to Prometheus]

---

## 5. Acceptance Criteria

| ID | Criterion | Verification Method |
|----|-----------|-------------------|
| AC-001 | [When X happens, system must do Y] | [e.g., Automated test / Manual QA] |
| AC-002 | [When A fails, system must respond with B] | [e.g., Integration test] |

---

## 6. Dependencies

### Internal

| Dependency | Type | Notes |
|------------|------|-------|
| [e.g., User module] | Hard dependency | Must be complete before implementation |
| [e.g., Auth middleware] | Hard dependency | JWT validation required |

### External

| Dependency | Type | Notes |
|------------|------|-------|
| [e.g., Stripe API v3] | External service | Rate limit: 100 req/s |

---

## 7. Constraints & Assumptions

### Constraints

- [e.g., Must use existing PostgreSQL database — no new DB allowed]
- [e.g., Cannot break the existing public API contract]

### Assumptions

- [e.g., Users are already authenticated when reaching this feature]
- [e.g., Product catalog is read-only during this operation]

---

## 8. Context & Decisions

[Reference to `.specs/features/[feature-name]/context.md` for gray-area decisions]

---

## 9. Traceability Matrix

| Requirement | Acceptance Criteria | Design Section | Task |
|-------------|-------------------|----------------|------|
| FEAT-XXX-REQ-001 | AC-001, AC-002 | design.md §3 | T-001 |

---

## 10. Open Questions

| # | Question | Owner | Status |
|---|----------|-------|--------|
| 1 | [Question] | [name] | Open |

---

## Change Log

| Date | Author | Change |
|------|--------|--------|
| [YYYY-MM-DD] | [name/agent] | Initial draft |
