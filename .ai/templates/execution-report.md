<!--
TEMPLATE — per-section execution report. Copy to
<artefact_root>/<slug>/tasks/execution-report-S<n>.md, delete this header, fill it in.
Written at step A10 / B9, one per section.

TWO THINGS THIS FILE EXISTS TO ENFORCE:
  1. GATE EVIDENCE, NOT A CLAIM. "Gates pass" is unfalsifiable. Paste the real output.
  2. A SECTION IS NOT COMPLETE WHILE A FOLLOW-UP IS OPEN. Mechanical, not a judgement call (F9).

It is also the RESUME POINT together with the status table in README.md. On "continue", the latest execution
report is read before anything else — never memory, never the conversation.

Sections are not deleted. Write None or Not applicable.
-->

# Execution Report — S<n> <section name>

| | |
|---|---|
| Section | S<n> |
| Requirements | R<n>, R<n> |
| Design | `../design/design-0N-<topic>.md` |
| Tasks | <n> planned, <n> completed |
| Started / completed | <date> / <date> |
| **Status** | **complete** / in progress / blocked |
| Open follow-ups | **<n>** — a non-zero value means this section is **not** complete |

## 1. Re-pinned at start

Confirming the anti-drift step actually happened (**F2**, **F10**).

| Pinned | Confirmed |
|--------|-----------|
| Feature steering file | ☐ |
| Requirement R-block | ☐ |
| This section's design doc | ☐ |

## 2. Task outcomes

| Task | Status | Files touched | Notes |
|------|--------|--------------|-------|
| T<n>.1 | done / partial / not done | | |

### Files changed

| File | Action | Lines | Why |
|------|--------|-------|-----|
| `path` | created / modified / — | +<n>/-<n> | |

**No deletions appear here.** A deletion in this table means a removal rode inside feature work (**F6**), which
is a finding, not a line item.

## 3. Deviations from design

Where the implementation differs from what the design said. Each needs the design document updated — an
undocumented deviation makes the design wrong for everyone who reads it next (**F1**).

| # | Design said | Built | Why | Document updated | Requirement amended |
|---|-------------|-------|-----|------------------|--------------------|
| 1 | | | | ☐ | ☐ / n/a |

## 4. Gate evidence 🔴

**Real output. Not a claim.**

### Lint

```
$ <command>
<actual output>
```

### Static / type

```
$ <command>
<actual output>
```

### Test

```
$ <command>
<actual output — including the pass/fail counts>
```

### Build

```
$ <command>
<actual output — tail is sufficient if it is long>
```

### Additional gates

| Gate | Command | Result |
|------|---------|--------|
| | | |

### Summary

| Gate | Result | Warnings |
|------|--------|----------|
| lint | ✅ / ❌ | **0** |
| static | ✅ / ❌ | |
| build | ✅ / ❌ | |
| test | ✅ / ❌ | <n> passed |

| | |
|---|---|
| Pre-existing failures, not caused here | <named, not absorbed> |
| Any gate weakened to pass? | **must be no** — a suppression, a widened type, or an excluded file is a finding |

## 5. Verification per requirement

Observably met, not plausibly met.

| Requirement | Criterion | How verified | Result |
|-------------|-----------|--------------|--------|
| R<n> | <n>.1 | <the test, or the observation> | ✅ |

## 6. Contracts preserved

**Mandatory on Track B.** One row per `do-not-break.md` row this section touched.

| Register row | Contract | Check run | Result |
|--------------|----------|-----------|--------|
| 2.3 | | | ✅ |

Any row that could not be verified locally is stated as a residual risk with the specific unverified
condition — not marked green.

## 7. Tests added

| Test | Level | Covers | Path |
|------|-------|--------|------|
| | | R<n> criterion <n>.1 | `path` |

| | |
|---|---|
| Pure logic extracted | `path` |
| Not automatable, and why | |
| Manual check recorded in | `manual-testing.md` |

## 8. 3× verification

| Pass | Result | Findings | Fixed |
|------|--------|----------|-------|
| P1 logic | ✅ / ❌ | | |
| P2 structure | | | |
| P3 instruction | | | |

| | |
|---|---|
| Anything discarded and regenerated? | <what, and why> |
| Second discard on the same artefact? | <if yes, that is **F3** — what changed in the approach> |

## 9. Follow-ups 🔴

**These block section closure.** Not "will get to it" — the section stays open.

| # | Description | Severity | Why not done now | Status |
|---|-------------|----------|------------------|--------|
| 1 | | 🔴 / 🟡 / 🟢 | | **open** / done |

| | |
|---|---|
| Open count | **<n>** |
| Section can close? | **only when this is 0** |

## 10. Deferred removals

| Thing | Why it can go | Consumers read? | Cutover row |
|-------|---------------|-----------------|-------------|
| | | ☐ | |

## 11. Discoveries

What was learned that changes what comes next. This is the most useful section for the next session.

| # | Discovery | Impact | Documents updated |
|---|-----------|--------|-------------------|
| 1 | | | |

### Failure signals that fired

Code, signal, move. The pattern across sections is worth more than any individual clean report.

| Code | Signal | Move taken |
|------|--------|-----------|
| F<n> | | |

### Documents healed

| Document | Was wrong how | Fixed | `INDEX.md` updated |
|----------|--------------|-------|-------------------|
| `path` | | | ☐ |

## 12. Corrections to earlier claims

| Claim | Stated in | Actually | Evidence | Re-walked |
|-------|-----------|----------|----------|-----------|
| | `relevant_info.md` §2 | | `path:line` | |

Also appended to the survey's corrections log — **F1** is fixed at the source, not downstream.

## 13. Status table updated

| | |
|---|---|
| `README.md` status table updated | ☐ |
| Status set to | |
| Next section | S<n> |
| Blockers for the next section | |

## 14. Section sign-off

| # | Condition | Met |
|---|-----------|-----|
| 1 | Every planned task done, or explicitly deferred with a reason | ☐ |
| 2 | All four gates green, real output pasted above | ☐ |
| 3 | Every requirement in this section observably verified | ☐ |
| 4 | Every touched `do-not-break.md` row green *(Track B)* | ☐ |
| 5 | **Zero open follow-ups** | ☐ |
| 6 | 3× verification complete | ☐ |
| 7 | Nothing from the do-not-regress register reintroduced | ☐ |
| 8 | No removal executed inside this section | ☐ |
| 9 | Documents healed and indexed | ☐ |
| 10 | Status table updated | ☐ |

**A section with any box unticked is not complete.** Reporting it complete anyway is **F9**.
