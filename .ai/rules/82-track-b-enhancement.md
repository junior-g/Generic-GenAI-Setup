# Track B — Enhancement

**Load mode: ON-DEMAND.** Pull in when the work changes an existing feature: a new field, state, or screen on
surfaces that already exist, where live data and existing contracts constrain the design.

Templates: [`../templates/README.md`](../templates/README.md). `<root>` means `<artefact_root>/<slug>`.

---

## What makes this track different from Track A

An enhancement's main risk is not *what to build*. It is **what breaks**.

So Track B adds three things Track A does not have: a mandatory `do-not-break.md` register, a mandatory
regression sweep inside the design review, and a lighter design stage — because the architecture already
exists and re-deriving it is waste.

## Round caps — hard, no escape hatch

| Stage | Full | B-lite |
|-------|------|--------|
| Gap analysis | **2 rounds** | **1 round** |
| Design review | **2 rounds** | one combined design + tasks round |
| Tasks review | **1 round** | — |

**B-lite** applies when the change is confined to one surface, with no schema change and no permission
change. **Ask before selecting it; do not assume it.** B-lite still requires `do-not-break.md` — that is the
one thing it never drops.

---

## Steps

### `B0` — Intake

Create `<root>/` with the filled
[`raw-requirement-enhancement.md`](../templates/raw-requirement-enhancement.md) at `<root>/<Enhancement>.md`.

Record explicitly **whether live data already exists** for the touched surfaces. If it does, back-compat and
migration are in scope by default, and saying so at intake is what stops them being discovered at execution.

The user's description of current behaviour is **a claim to be verified**, not an instruction. Being wrong
there is useful — it gets caught at `B1` instead of at runtime.

### `B1` — Two artefacts

**`<root>/relevant_info.md`** ([template](../templates/relevant-info.md)) — grounding, limited to the touched
surfaces. Targeted queries, every claim a `path:line` pointer verified by reading. Negatives need two
independent checks by different methods.

**`<root>/do-not-break.md`** ([template](../templates/do-not-break.md)) — **mandatory.** Every existing
contract, its consumers, and the check that proves it still holds:

| Contract type | What to capture |
|---------------|-----------------|
| Routes / screens | path, who links to it, what state it expects |
| API responses | every field a consumer reads, and which consumer |
| Events / messages | payload shape, every subscriber |
| Schema fields | every reader and writer, plus records that predate the field |
| Permissions | which actor reaches which entry point, and how |
| Localised strings | every key a component still reads |
| Shared components and styles | every usage site |
| Existing tests | which ones assert the behaviour you are changing |
| Public exports | anything outside the module that imports it |

Contracts you intend to break deliberately go in **their own table**, with a migration path and the user's
approval. A deliberately broken contract is a decision; a silently broken one is an outage.

> Fill this file by **reading consumers, not searching for them.** The two most expensive wrong claims in
> this framework's history were both "removing this is impact-free", both were scoped-search negatives, and
> both survived two review stages. This is **F5**; if a claim here is later proved wrong it becomes **F1** —
> fix it at the source and re-walk everything derived from it.

### `B2` — Context anchor → `<rules_root>/8x-<slug>.md`

[Template](../templates/feature-steering.md), including the must-not-break summary and, if permissions are
touched, the entry-points table. Pull in at every session start and section boundary.

### `B3` — Gap analysis → `<root>/all_gaps.md`, max 2 rounds

[Template](../templates/all-gaps.md). **STOP after each round.**

Two rounds, not three, because the design space is already constrained by what exists. If two rounds are not
enough, that is a signal the work is actually Track A — say so rather than requesting a third.

### `B4` — Settled requirement → `<root>/final_requirement.md`

[Template](../templates/final-requirement.md), with two mandatory extras:

- **Unchanged behaviour** — a section summarising `do-not-break.md`, so the requirement itself states what
  must still be true afterwards.
- **Migration / back-compat plan**, whenever live data exists: what happens to records without the new
  field · the read path during rollout · the write path during rollout · the reversal path · how the
  migration is verified, and what cannot be verified without production-like data.

**STOP for user review.** Amendments after approval, never silent edits.

### `B5` — Design → `<root>/design/`

One `design-0N-<topic>.md` per changed surface, two to four maximum, each with an **old → new delta table**.
A delta table is the whole point: it makes the change reviewable without re-reading the original design.

`00-architecture-overview.md` only if more than two surfaces interact. Otherwise it is ceremony.

### `B6` — Design review, max 2 rounds — one must be the regression sweep

[Template](../templates/review-round.md).

**The regression sweep is mandatory and is not optional in either round.** For every symbol, route, field,
key, or component being changed: find the consumers and **read them**. Contracts discovered this way are
appended to `do-not-break.md` — the register is **not frozen** after `B1`.

Rounds emit numbered `DR-n` findings or sign off naming every file read. **STOP.**

### `B7` — Task plan → `<root>/tasks/`

[`tasks-readme.md`](../templates/tasks-readme.md) + [`task-details.md`](../templates/task-details.md).

- `S0` is types, schema and the data layer.
- Then one section per surface.
- Plus a **dedicated regression section** covering every row of `do-not-break.md`. Not a checklist item at
  the end — its own section, with its own tasks.
- **Non-destructive ordering**: the new path ships and is verified before the old one is touched.

### `B8` — Tasks review, 1 round

Then consolidate and write `<root>/final-review-report.md`
([template](../templates/final-review-report.md)). **STOP.**

### `B9` — Execute per section

Re-pin the feature steering file, the relevant `R`-block, and that section's design doc. Run all four gates.
Write `<root>/tasks/execution-report-S<n>.md` ([template](../templates/execution-report.md)) **including the
contracts-preserved table** — one row per `do-not-break.md` row this section touched, with the check that
proved it.

Burn down follow-ups before the next section.

### `B10` — Cutover, separate approval → `<root>/tasks/cutover-report.md`

[Template](../templates/cutover-report.md). Removals and retirements only, after every feature section is
green. Per item: pre-flight checklist · consumers found **and read** · an **unscoped** search, not just a
scoped one · reversal path · the user's individual approval. Gates after each item.

### `B11` — Close out

Manual testing executed · docs synced · captures for changed surfaces · index rebuilt if tier 2 ·
`INDEX.md` Zone 2 updated · **feature steering file deleted**.

---

## Enhancement-specific traps

Each of these has actually happened. The guard is what the track adds.

| Trap | Guard |
|------|-------|
| A field made required breaks every existing record and caller that omits it | ripple-effects table per task; migration plan at `B4` |
| An actor gains access to a surface but no entry point, so the capability is stranded | entry-points table in `do-not-break.md` — menus, hubs, shared role lists |
| A localised key deleted while a component still reads it | the localisation table in `do-not-break.md` |
| A style or class referenced on a surface that never loads the stylesheet | the styling row in the design section checklist |
| A test edited during implementation to make the suite pass | a test that must change is a **contract change**, justified in advance |
| An "impact-free" removal that a live surface still depends on | cutover pre-flight: consumers **read**, not searched |
| A new field defaults sensibly in code but not in the store, so old records read as garbage | migration plan states the read path for records predating the field |
| A response field renamed while a client still reads the old name | API table in `do-not-break.md`, plus a deprecation window rather than a rename |
| Two code paths now write the same data with different validation | the regression section covers write paths, not just reads |

## Definition of done

1. Every requirement's acceptance criteria observably met.
2. **Every row of `do-not-break.md` verified green**, with the check named.
3. All four gates green, with real output.
4. Locale parity for user-facing strings, if applicable.
5. Nothing from [`70-do-not-regress.md`](70-do-not-regress.md) reintroduced.
6. Migration verified — or its unverifiable-locally limits stated as an explicit residual risk.
7. 3× verification complete.
8. Docs synced, `INDEX.md` current, index rebuilt, feature steering file retired.

## What still goes wrong on Track B

| Risk | Why it survives | What compensates |
|------|-----------------|------------------|
| `do-not-break.md` is only as good as the user's list plus your sweep | an unknown consumer — a live surface reading data nobody remembered — is the classic miss | the `B6` sweep reads consumers rather than searching; cutover pre-flight requires an unscoped search |
| Live-data migration correctness cannot be fully verified locally | no production-like dataset | state it as a residual risk with the specific unverified condition, and stage the rollout |
| Localisation parity drifts | secondary locales lag the primary one | an explicit parity check in the regression section |
| A contract exists only in a consumer's expectations, nowhere in code | nothing to search for | ask the user what depends on this; their answer is a lead, then verified |
