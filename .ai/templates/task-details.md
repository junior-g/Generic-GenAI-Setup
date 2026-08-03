<!--
TEMPLATE — task inventory. Copy to <artefact_root>/<slug>/tasks/task_details.md, delete this header.
Written at step A7 / B7, then extended one section at a time.

LAZY ELABORATION IS THE POINT. Only the section ABOUT TO RUN gets full task blocks. Every later section
carries an inventory line and a design reference until its turn. Elaborating everything up front produces
detail that the first section's discoveries invalidate — that is paying twice for the same plan (F7).

THE RIPPLE-EFFECTS TABLE PER TASK IS WHAT CATCHES CASCADING BREAKAGE (F1). A field made required breaks every
caller that omits it, and the ripple table is where that gets noticed before it happens rather than after.

Sections are not deleted. Write None or Not applicable.
-->

# Task Details — <slug>

| | |
|---|---|
| Map | `README.md` — status table is the resume point |
| Requirement | `../final_requirement.md` v<n> |
| **Fully elaborated** | S<n> |
| **Inventory only** | S<n+1>…S<n> |

## Inventory — all sections

The complete list, at one line per task. Elaboration comes later, per section.

| Section | Task | Does | Requirement | Design | Discipline | Elaborated |
|---------|------|------|-------------|--------|-----------|-----------|
| S0 | T0.1 | | R<n> | | database | ✅ |
| S1 | T1.1 | | R<n> | `design-01` §4 | | ☐ |

---

# S<n> — <section name>

Elaborated because it is next. Format: pick the discipline format from
[`task-formats/README.md`](task-formats/README.md) — path relative to `.ai/templates/`.

| | |
|---|---|
| Requirements | R<n>, R<n> |
| Design | `../design/design-0N-<topic>.md` |
| Discipline | |
| Depends on | S<n> |
| Tasks | <n> |
| Re-pin before starting | feature steering file · R-block · this section's design doc |

## T<n>.<m> — <task name>

| | |
|---|---|
| **Requirement** | R<n> — acceptance criteria <n>.1, <n>.2 |
| **Design** | `design-0N` §<n> |
| **Discipline** | |
| **Reversibility** | pure / reversible / compensable / **irreversible** |
| **Depends on** | T<n>.<m> |

### Files touched

| File | Action | Why |
|------|--------|-----|
| `path` | create / modify | |

Every `modify` row means the file was **read** first. Modifying a file you have not read is **F5** with a
compile error attached if you are lucky.

### Plan

1. <step>
2. <step>
3. <step>

Concrete enough that someone else could execute it. "Implement the handler" is not a step.

### Ripple effects 🔴

**What else is affected by this change.** The table that catches cascading breakage before it happens.

| Affected | Path | How | Handled by |
|----------|------|-----|-----------|
| | `path:line` | | this task / T<n>.<m> / already safe because… |

Check specifically: callers of a changed signature · readers of a changed field · fixtures and factories ·
existing tests · type consumers · anything that constructs this shape · anything that mocked it.

### Discipline checklist

Fill from the matching task format. The rows that apply, with a result.

| # | Check | Result |
|---|-------|--------|
| | | |

### Test plan

| # | Test | Level | Asserts | Acceptance criterion |
|---|------|-------|---------|---------------------|
| 1 | | unit / integration / manual | | <n>.1 |

| | |
|---|---|
| Pure logic extracted to | `path` |
| Not automatable, and why | |
| Manual check standing in | |

### Done when

| # | Criterion |
|---|-----------|
| 1 | <observable outcome> |
| 2 | Its acceptance criteria observably met |
| 3 | All four gates green |
| 4 | Ripple effects handled or explicitly safe |
| 5 | No follow-up left open |

### Notes

<Anything discovered while planning that the executor needs.>

---

## T<n>.<m+1> — <task name>

<Same structure. One block per task.>

---

## Section-level checks

Run once for the whole section, after every task.

| # | Check | Result |
|---|-------|--------|
| 1 | All four gates green, real output | |
| 2 | Every requirement in this section observably satisfied | |
| 3 | Every `do-not-break.md` row this section touched verified *(Track B)* | |
| 4 | Nothing from the do-not-regress register reintroduced | |
| 5 | Removals recorded and deferred, not executed | |
| 6 | New logic tested; new boundaries integration-tested | |
| 7 | Docs touched by this section healed and indexed | |
| 8 | 3× verification complete | |
| 9 | Zero open follow-ups | |

## Deferred to cutover

| Thing | Why it can go | Consumers read? |
|-------|---------------|-----------------|
| | | ☐ |

## Discoveries

Things learned while executing that change what comes next. Copied into the execution report.

| # | Discovery | Impact | Documents updated |
|---|-----------|--------|-------------------|
| 1 | | | |

Include any `F<n>` signal that fired and changed what you did: the code, the signal, the move.
