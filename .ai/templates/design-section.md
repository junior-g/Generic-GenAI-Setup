<!--
TEMPLATE — section design. Copy to <artefact_root>/<slug>/design/design-0N-<topic>.md, delete this header.
Written at step A5 / B5. One per surface or coherent concern.

THE SECTION ORDER BELOW IS DELIBERATE AND IS NOT TO BE REARRANGED:
  surfaces → components → REUSE DECISION → boundary split → validation.
Reuse is decided AFTER components are enumerated. Deciding it first produces a design shaped by what already
exists rather than by what is needed.

TRACK B: the old → new delta table is mandatory. It is what makes the change reviewable without re-reading the
original design.

Sections are not deleted. Write None or Not applicable.
-->

# Design <0N> — <topic>

| | |
|---|---|
| Requirements | R<n>, R<n> |
| Overview | `00-architecture-overview.md` <or "none — single-section change"> |
| Discipline | UI / backend / database / infrastructure / data / integration |
| Status | draft / reviewed round <n> / approved |
| Task section | S<n> |

## 1. Purpose

<Two or three sentences. What this section delivers and which requirements it satisfies.>

## 2. Delta — old → new

**Mandatory on Track B.** Optional on Track A where the section modifies something that exists.

| Aspect | Today | After | Requirement | Breaking? |
|--------|-------|-------|-------------|-----------|
| | `path:line` | | R<n> | yes / no |

Any "breaking: yes" row must already appear in `../do-not-break.md` §11 with an approved migration path.

## 3. Surfaces

| Surface | Kind | Actor | Purpose | Requirement |
|---------|------|-------|---------|-------------|
| | | | | R<n> |

### States

Every surface's full state set. The unglamorous states are where defects live.

| Surface | Loading | Empty | Populated | Error | Unauthorised | Partial |
|---------|---------|-------|-----------|-------|--------------|---------|
| | | | | | | |

## 4. Components

Enumerate what is needed, before deciding what to reuse.

| Component | Responsibility | Consumes | Produces | New? |
|-----------|---------------|----------|----------|------|
| | | | | |

```mermaid
graph TD
```

## 5. Reuse decision

Now that the components are known, decide each one.

| Component needed | Existing candidate | Verdict | Why |
|------------------|-------------------|---------|-----|
| | `path:line` | reuse as-is / extend / new | |

| | |
|---|---|
| Extending an existing component — what breaks? | <every current usage site, read> |
| New shared component — why not local? | |

## 6. Boundary split

Which side of each boundary the work sits on. The boundary depends on the platform: server/client,
process/worker, build-time/request-time, trusted/untrusted.

| Component | Side | Why | Consequence of the wrong side |
|-----------|------|-----|------------------------------|
| | | | |

Default to the cheaper side. Crossing a boundary for convenience is a cost paid on every request, forever, and
nobody notices because it works.

## 7. Data contract

| Field | Type | Source | Required | Validation | Where validated |
|-------|------|--------|----------|-----------|-----------------|
| | | | | | `path` |

**Read the real declarations.** Guessing a shape is **F13**, and widening a type to make the static gate pass
converts a caught failure into an uncaught one.

## 8. Behaviour

| # | Trigger | Precondition | Behaviour | Postcondition | Requirement |
|---|---------|--------------|-----------|---------------|-------------|
| | | | | | R<n> |

### Error and edge behaviour

| Condition | Behaviour | User sees |
|-----------|-----------|-----------|
| Empty / missing input | | |
| Boundary value | | |
| Duplicate action | | |
| Unauthorised actor | | |
| Concurrent modification | | |
| Dependency unavailable | | |
| Timeout | | |

## 9. Validation diagram

How a request or action is validated end to end. Drawing it is what reveals the missing check.

```mermaid
flowchart TD
```

| Check | Where | On failure |
|-------|-------|-----------|
| | `path` | |

## 10. Authorisation

| Action | Actor allowed | Check | Ownership scoping |
|--------|--------------|-------|-------------------|
| | | `path` | |

## 11. Discipline-specific detail

Fill the block matching this section's discipline; the others are `Not applicable`. Full checklists:
[`task-formats/README.md`](task-formats/README.md) — path relative to `.ai/templates/`.

**UI** — tokens used (no raw values) · layout classes reused · responsive behaviour at the narrowest supported
width · minimum interactive target · accessibility (focus, labels, contrast, keyboard order) · which stylesheet
must be loaded on this surface · how the styling gets visually validated.

**Backend** — handler shape followed · status codes per outcome · idempotency · rate limit · error envelope ·
what is logged and what must never be.

**Database** — schema delta · migration direction and reversibility · indexes and the queries they serve ·
backfill (idempotent, batched, resumable) · behaviour for records predating the change.

**Infrastructure** — resources created · what is destroyed or replaced in place · plan reviewed before apply ·
blast radius · rollback path.

**Data / ML** — inputs and their provenance · determinism and seeding · evaluation metric and threshold ·
drift detection · reproducibility.

**Integration** — third-party contract with a link to its docs · authentication · retry and backoff ·
idempotency key · webhook verification · failure and replay behaviour.

## 12. Reversibility

| Action | Class | Approval | Cutover? |
|--------|-------|----------|----------|
| | pure / reversible / compensable / **irreversible** | ☐ | |

## 13. Test plan

| # | What | Level | Asserts | Requirement |
|---|------|-------|---------|-------------|
| | | unit / integration / manual | | R<n> |

| | |
|---|---|
| Pure logic extracted for testing | <what, and where it moves to> |
| Cannot be automated, and why | |
| Manual check standing in | |

## 14. Out of scope for this section

- <thing>

## 15. Open questions

| # | Question | Blocking? | Default |
|---|----------|-----------|---------|
| | | | |

## 16. Review findings folded in

Filled during design review. Empty until then.

| Finding | Round | Change made |
|---------|-------|-------------|
| DR-<n> | | |
