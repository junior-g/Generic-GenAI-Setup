<!--
TEMPLATE — bug fix report. Copy to <artefact_root>/hotfixes/<slug>/fix-report.md, delete this header.

STARTED AT STEP C2 — BEFORE ANY CODE IS TOUCHED. §3 root cause is filled first. THE REPORT EXISTING BEFORE THE
FIX IS WHAT ENFORCES THE GATE; it cannot be satisfied retroactively, which is the entire point of the ordering.

THREE THINGS THIS FILE ENFORCES:
  1. A written root cause before the first edit — mechanism, exact path:line, and why it manifests only under
     the broken condition. All three. A cause that does not explain the works/broken contrast is not the cause.
  2. Fails-before / passes-after, BOTH OBSERVED. A test written after the fix that has never been red proves
     nothing about the defect.
  3. Two-strike attempt log. Two failed attempts ends the approach, not just the attempt (F3).

Sections are not deleted. Write None or Not applicable.
-->

# Fix Report — <slug>

| | |
|---|---|
| Bug report | `README.md` |
| Severity | 🔴 / 🟡 / 🟢 |
| Reported | <date> |
| Fixed | <date> |
| Reversibility of the fix | pure / reversible / compensable / irreversible |
| Attempts | <n> |
| Status | root cause identified / fixed / **cannot reproduce — stopped** |

## 1. Symptom

<One paragraph, from the bug report. Observable behaviour only.>

## 2. Reproduction — confirmed

`C1`. Observed, not accepted on description.

| | |
|---|---|
| Reproduced? | ✅ yes / ❌ no |
| Environment | <build, version, how started> |
| Steps followed | <as reported / adjusted — say how> |
| Observed behaviour | |
| Reproduces every time? | |

### If it did not reproduce

| What was tried | Result | What it rules out |
|----------------|--------|-------------------|
| | | |

**STOP and ask for more evidence.** Do not fix speculatively — an unreproduced fix cannot be verified, and
edits to working code are a net loss (**F5 F8**).

---

## 3. Root cause 🔴

**Filled at `C2`, before any code edit.** All three parts.

### Mechanism

<What the code actually does that produces the symptom. Not "the handler is wrong" — *the handler compares the
raw input against the normalised stored value, so any input differing in case does not match*.>

### Location

| | |
|---|---|
| **File and line** | `path/file.ext:LINE` |
| Confirmed by reading | ☐ |
| The relevant lines | see quote below |

```
<the actual code, quoted>
```

### Why only under the broken condition 🔴

<Tie it to the works/broken matrix. Why does the working condition work?>

| Condition | Behaviour | Explained by this cause? |
|-----------|-----------|-------------------------|
| <broken condition> | fails | ✅ |
| <working condition> | works | ✅ |

**A cause that does not explain the contrast is not yet the cause.** This is the check that catches
plausible-but-wrong causes, and it is the one most often skipped.

### Evidence trail

| # | What was checked | Where | What it showed |
|---|-----------------|-------|----------------|
| 1 | | `path:line` | |

### Rejected hypotheses

As valuable as the accepted one, and free to record while fresh. Stops a later session re-walking dead ends.

| # | Hypothesis | Why rejected | Evidence |
|---|-----------|--------------|----------|
| 1 | | | `path:line` |

---

## 4. Blast radius

`C3`. Consumers found and **read**.

| Consumer | Path | Read? | Affected by the fix? |
|----------|------|-------|---------------------|
| | `path:line` | ✅ | |

| | |
|---|---|
| Existing tests covering this path | `path:line` |
| Does the same mechanism exist elsewhere? | <listed below as follow-ups — **not fixed here**> |
| Fix stays inside the fence from `README.md`? | ☐ |

### Same mechanism elsewhere

| Location | Same cause? | Action |
|----------|------------|--------|
| `path:line` | | follow-up, not this change |

A cause that is a *class* of mistake usually has siblings. Listing them is right; fixing them here is scope
creep in a hotfix.

## 5. The fix

`C4`. Only what the root cause requires.

| File | Change | Lines |
|------|--------|-------|
| `path` | | +<n>/-<n> |

```diff
<the actual change>
```

### Why this is minimal

<Why nothing smaller would work, and nothing larger was needed.>

### Noticed but not changed

Improvements spotted in passing. **Listed, not made.** A hotfix that also tidies three things cannot be
reviewed, reverted, or attributed.

| # | What | Why not now |
|---|------|-------------|
| 1 | | |

## 6. Regression test 🔴

`C5`. Both states observed.

| | |
|---|---|
| Test | `path:line` |
| Level | unit / integration |
| Asserts | |

### Fails before the fix

```
$ <test command, with the fix reverted or stashed>
<actual output showing the failure>
```

### Passes after the fix

```
$ <test command, fix restored>
<actual output showing the pass>
```

**Both blocks must contain real output.** A test that has never been seen red proves only that the code does
what the code does.

### If it cannot be automated

| | |
|---|---|
| Why not | <real device input / actual delivery / rendered appearance / generated binary / live third party> |
| Manual check instead | <exact steps> |
| Executed, result | |

## 7. Verification

`C6`.

### Gates

```
$ <lint>
$ <static>
$ <build>
$ <test>
```

| Gate | Result | Warnings |
|------|--------|----------|
| lint | ✅ / ❌ | **0** |
| static | | |
| build | | |
| test | | <n> passed |

### Original reproduction re-run

| | |
|---|---|
| Steps re-run | ☐ |
| Symptom gone | ☐ |
| Output | |

### Working condition still works 🔴

| | |
|---|---|
| Condition checked | <from the works/broken matrix> |
| Still works | ☐ |
| Output | |

**A fix that inverts the bug is not a fix.** This is the check that catches it.

### 3× verification

| Pass | Result | Findings |
|------|--------|----------|
| P1 logic | | |
| P2 structure | | |
| P3 instruction — inside the fence, nothing extra | | |

## 8. Attempt log 🔴

**Every attempt, including failures.** Two failed attempts triggers the circuit breaker.

| # | Attempt | Result | **What it rules out** |
|---|---------|--------|----------------------|
| 1 | | | |
| 2 | | | |

### If two attempts failed

| | |
|---|---|
| Two-strike breaker tripped | ☐ |
| What the two failures together rule out | |
| Returned to `C2` and re-derived the cause | ☐ |
| The **new** theory, and how it differs at the level of the theory | |

**Do not attempt a third variation of the same theory.** The useful output of two failures is the set of
explanations they eliminate (**F3**).

## 9. Hardening

`C7`.

| # | Action | Done |
|---|--------|------|
| 1 | Was this a reintroduction, or a repeatable class of mistake? | <yes/no> |
| 2 | Do-not-regress row added | ☐ / n/a |
| 3 | A document contradicted the root cause — healed | ☐ / n/a |
| 4 | `INDEX.md` updated | ☐ |
| 5 | Retrieval index rebuilt *(tier 2)* | ☐ |

### Do-not-regress row added

| Don't | Do instead | Source |
|-------|-----------|--------|
| | | this fix report |

## 10. Follow-ups

| # | Description | Severity | Raised as |
|---|-------------|----------|-----------|
| 1 | | | hotfix `<slug>` / Track B enhancement / accepted |

## 11. Sign-off

| # | Condition | Met |
|---|-----------|-----|
| 1 | Root cause written **before** the first edit | ☐ |
| 2 | The cause explains the works/broken contrast | ☐ |
| 3 | Fix is minimal and inside the fence | ☐ |
| 4 | Regression test fails-before / passes-after, both pasted | ☐ |
| 5 | All four gates green | ☐ |
| 6 | Original reproduction no longer reproduces | ☐ |
| 7 | Previously-working condition still works | ☐ |
| 8 | Nothing from the do-not-regress register reintroduced | ☐ |
| 9 | Hardening done | ☐ |
| 10 | Noticed-but-not-changed items listed, not silently fixed | ☐ |
