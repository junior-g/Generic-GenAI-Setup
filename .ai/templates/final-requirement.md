<!--
TEMPLATE — settled requirement. Copy to <artefact_root>/<slug>/final_requirement.md, delete this header.
Written at step A4 / B4. Filled by the AGENT, approved by the USER.

THIS IS THE CONTRACT. Design, tasks, tests and the final traceability matrix all reference the R-numbers
here. Everything built must trace to one; anything that does not is scope creep (F2).

STOP FOR USER REVIEW BEFORE DESIGN BEGINS.

AFTER APPROVAL, CHANGES ARE AMENDMENTS IN §15 — NEVER SILENT EDITS. A silently edited requirement makes every
review that cited it wrong (F1), and there is no way to tell afterwards which version was reviewed.

R-numbers are stable and never renumbered. A dropped requirement is marked withdrawn, keeping its number.

Sections are not deleted. Write None or Not applicable.
-->

# Final Requirement — <slug>

| | |
|---|---|
| Track | A / B / B-lite |
| Version | 1.0 |
| Status | **awaiting approval** / approved <date> |
| Grounded on | `relevant_info.md` <, `do-not-break.md`> |
| Gaps closed | <n> of <n> · open with defaults: <n> |
| Requirements | R1–R<n> |

## 1. AIM

<Two to four sentences. The objective, as it will be re-read at every session start.>

## 2. Scope

### In scope

- <thing>

### Out of scope

**Binding on the implementation.** Anything here appearing in the work is scope creep.

- <thing>

### Deferred to a later phase

| Thing | Why deferred | Revisit when |
|-------|-------------|--------------|
| | | |

## 3. Actors

| Actor | Capability added | Entry point | Authorisation check |
|-------|-----------------|-------------|--------------------|
| | | | |

## 4. Requirements

Each `R` is atomic, testable, and traceable. Acceptance criteria are **observable** — a criterion nobody can
check is not a criterion.

### R1 — <short title>

| | |
|---|---|
| **Statement** | The system shall <behaviour>. When <trigger>, the system shall <response>. |
| **Actor** | |
| **Surface** | |
| **Priority** | 🔴 must / 🟡 should / 🟢 could |
| **Source** | raw requirement item <n> / gap G2.1 / design finding DR-3 |

**Acceptance criteria**

| # | Given | When | Then | Verified by |
|---|-------|------|------|-------------|
| 1.1 | | | | unit / integration / manual |

**Error and edge behaviour** — the section most often left thin, and the one that produces the most rework.

| Condition | Behaviour |
|-----------|-----------|
| Empty input | |
| Missing / null value | |
| Boundary value (zero, one, maximum) | |
| Duplicate submission | |
| Unauthorised actor | |
| Concurrent modification | |
| Downstream dependency unavailable | |

**Out of scope for R1**

- <thing>

---

### R2 — <short title>

<Same structure. One block per requirement.>

---

## 5. Unchanged behaviour

**Mandatory on Track B.** Summarises `do-not-break.md` so the requirement itself states what must still be
true afterwards.

| Contract | Must still | Verified by |
|----------|-----------|-------------|
| | | |

## 6. Data model

### Entities

| Entity | New / changed | Purpose |
|--------|--------------|---------|
| | | |

### Fields

| Entity | Field | Type | Required | Default | Notes |
|--------|-------|------|----------|---------|-------|
| | | | | | |

Every new entity carries the project's audit fields. See `<rules_root>/30-data-and-types.md`.

### Relationships

```mermaid
erDiagram
```

### State machines

One per entity with a lifecycle. An undrawn state machine is where illegal transitions live.

```mermaid
stateDiagram-v2
```

| From | To | Trigger | Who | Guard |
|------|----|---------|-----|-------|
| | | | | |

### Indexes

| Entity | Index | Supports |
|--------|-------|----------|
| | | |

## 7. Migration and back-compat

**Mandatory when live data exists.**

| | |
|---|---|
| Records affected | <count, or how to find out> |
| Records predating the change read as | |
| Read path during rollout | |
| Write path during rollout | |
| Backfill required? | <and whether it is idempotent, batched, resumable> |
| Reversal path | |
| Verified how | |
| **Cannot be verified locally because** | <the specific unverifiable condition — carried as a residual risk> |

## 8. Interface surface

| Operation | Kind | Actor | Input | Output | Errors | Requirement |
|-----------|------|-------|-------|--------|--------|-------------|
| | endpoint / command / job / consumer | | | | | R<n> |

## 9. Surface inventory

| Surface | Kind | Actor | Purpose | Requirement |
|---------|------|-------|---------|-------------|
| | | | | R<n> |

## 10. Flows

```mermaid
sequenceDiagram
```

| Step | Actor | Action | System response | Requirement |
|------|-------|--------|-----------------|-------------|
| | | | | |

## 11. Non-functional requirements

Only real constraints. An invented budget is worse than none.

| # | Concern | Requirement | Measured how |
|---|---------|-------------|--------------|
| N1 | Performance | | |
| N2 | Security | | |
| N3 | Accessibility | | |
| N4 | Observability | | |
| N5 | Availability | | |

## 12. Gaps still open — decided by default

Carried from `all_gaps.md`. Each has a reversible default so the work can proceed without pretending the
question is settled.

| Gap | Question | Options | **Default applied** | Reversible? | Risk if wrong |
|-----|----------|---------|--------------------|-------------|---------------|
| | | A / B | A | yes | |

## 13. Phasing

| Phase | Contains | Requirements | Independently shippable? |
|-------|----------|--------------|-------------------------|
| 1 | | R1, R2 | |

## 14. Definition of done

| # | Criterion | Verified by |
|---|-----------|-------------|
| 1 | Every requirement's acceptance criteria observably met | traceability matrix |
| 2 | All four gates green, real output shown | execution reports |
| 3 | New logic unit-tested; new boundaries integration-tested | |
| 4 | Every row of `do-not-break.md` verified green *(Track B)* | regression section |
| 5 | <locale parity, if applicable> | |
| 6 | Nothing from the do-not-regress register reintroduced | final review |
| 7 | 3× verification complete | |
| 8 | Docs synced, index current, feature steering file retired | close-out |

## 15. Amendment log

**The only legitimate way to change this document after approval.** An edit with no row here is a wrong
premise with no trail.

| # | Date | Requirement | Change | Reason | Impact on design / tasks | Approved |
|---|------|-------------|--------|--------|--------------------------|----------|
| A1 | | R<n> | | | | ☐ |

## 16. Approval

| | |
|---|---|
| Reviewed by | |
| Date | |
| Verdict | approved / changes requested |
| Conditions | |

**STOP — design does not begin until this is approved.**
