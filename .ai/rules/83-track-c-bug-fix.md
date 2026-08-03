# Track C — Bug Fix

**Load mode: ON-DEMAND.** Pull in when shipped behaviour is wrong and reproducible.

No gap rounds. No design documents. The whole track is a funnel to a **verified root cause**, then the
smallest change that removes it.

Templates: [`raw-requirement-bug-fix.md`](../templates/raw-requirement-bug-fix.md) for intake and
[`fix-report.md`](../templates/fix-report.md) for everything after. `<root>` means
`<artefact_root>/hotfixes/<slug>`.

---

## Two hard gates

1. **No code edit before a written root cause.** Mechanism, exact `path:line`, and why it manifests only
   under the broken condition. All three, in writing, before the first edit.
2. **Cannot reproduce → stop and ask.** Never fix speculatively. An unreproduced fix cannot be verified, and
   edits to working code are a net loss (**F5 F8**).

These are gates, not guidelines. The fix report existing before the fix does is the mechanism that enforces
gate 1 — it cannot be satisfied retroactively.

---

## Steps

### `C0` — Intake → `<root>/README.md`

From the [template](../templates/raw-requirement-bug-fix.md): symptom · **works/broken matrix** ·
reproduction steps · expected vs actual · evidence · and **the fence** (what the fix may and may not touch).

The works/broken matrix is the highest-value block in the whole track. "Broken on mobile, fine on desktop"
or "fails for the second user in a session, not the first" often localises the cause to one file before any
code is read.

### `C1` — Reproduce

Follow the stated steps and confirm the actual behaviour **yourself**. Not "the description is plausible" —
observed.

If it does not reproduce: record what you tried, what that rules out, and **STOP and ask** for more
evidence. Environment, version, data state, timing, and permissions are the usual missing variables.

Note which environment you reproduced in. A defect reproduced only in a production-like build and not in
development is itself a finding, and it changes how the fix must be verified.

### `C2` — Root cause → start `<root>/fix-report.md`

Fill the root-cause section **before touching any code**.

- **Mechanism** — what the code actually does that produces the symptom. Not "the handler is wrong": *the
  handler compares the raw input against the normalised stored value, so any input with different casing
  misses.*
- **Location** — `path/file.ext:LINE`, confirmed by reading the file.
- **Why only under the broken condition** — tie it to the works/broken matrix. **A cause that does not
  explain the contrast is not yet the cause.** This is the check that catches plausible-but-wrong causes,
  and it is the one most often skipped.

Also record the **evidence trail** and the **rejected hypotheses**, so a later session does not re-walk dead
ends. Rejected hypotheses are as valuable as the accepted one and cost nothing to write down while they are
fresh.

### `C3` — Blast radius

Find the consumers of the code path and **read them**. Then:

- Classify the fix: pure / reversible / compensable / irreversible.
- Note existing tests covering the path — they tell you what behaviour is currently guaranteed.
- Confirm the fix stays inside the fence from `C0`.
- Check whether the same mechanism exists elsewhere. A cause that is a *class* of mistake usually has
  siblings; list them as follow-ups, do not fix them in this change.

### `C4` — Minimal fix

Only what the root cause requires. No refactors, no adjacent cleanups, nothing outside the fence.

Improvements noticed in passing are **listed in the report, not made**. A hotfix that also tidies three
things cannot be reviewed, cannot be reverted cleanly, and cannot be attributed when something else breaks.

### `C5` — Regression test

Must **fail before** the fix and **pass after** — both confirmed by actually running it in each state. A test
written after the fix that has never been seen red proves nothing about the defect; it proves the code
currently does what the code currently does.

The practical sequence: write the test, revert the fix (or stash it), run the test and see it fail, restore
the fix, run it and see it pass. Paste both results.

If genuinely not automatable — real device input, actual mail delivery, generated binary output, live
payment flow, a viewport behaviour the test environment cannot render — say **why**, and add a manual check
to the report instead. Do not simulate the untestable part and claim coverage of it.

### `C6` — Verify and report

- All four gates (`config.yml` → `gates`), real output.
- **Re-run the original reproduction.** The symptom is gone.
- **Confirm the *working* condition from the matrix still works.** A fix that inverts the bug is not a fix,
  and this is the check that catches it.
- Complete `<root>/fix-report.md`.
- 3× verification — [`62-3x-verification.md`](62-3x-verification.md).

### `C7` — Harden

- If this was a reintroduction, or a repeatable class of mistake, add a row to
  [`70-do-not-regress.md`](70-do-not-regress.md) Part 2 with the source.
- If a document contradicted the root cause, heal it — [`65-auto-healing-docs.md`](65-auto-healing-docs.md).
- Rebuild the retrieval index if tier 2 and others will search this code.
- Add the `INDEX.md` Zone 2 row.

---

## Two-strike circuit breaker

If two fix attempts fail the `C1` reproduction: **stop editing.**

1. Log both attempts and what they **rule out** in the fix report's attempt log.
2. Return to `C2` and re-derive the root cause from scratch.
3. Do not attempt a third variation of the same theory.

Continuing to patch past two failures is exactly the loop this rule exists to break (**F3**). The negative
information from two failed attempts is the most useful thing you have; writing it down is what converts a
loop into progress.

## Severity handling

| Severity | Root-cause gate | Regression test | Notes |
|----------|-----------------|-----------------|-------|
| 🔴 blocks a user journey | required | required | fix now, one defect per session |
| 🟡 degrades a journey | required | required | |
| 🟢 cosmetic | required | required, or a written justification | may be batched with other 🟢 fixes **only if they share a root cause** |

**The gates do not relax with severity, and they do not relax with urgency.** A cosmetic symptom can have a
structural cause, and a production incident is precisely when a speculative fix is most expensive. Urgency
changes what you work on, never how you verify it.

## Definition of done

1. The reproduction from the report no longer produces the reported behaviour.
2. The previously-working condition from the matrix still works.
3. A regression test **fails-before / passes-after**, both observed and pasted — or a justified manual check.
4. All four gates green, with real output.
5. Nothing from [`70-do-not-regress.md`](70-do-not-regress.md) reintroduced.
6. `fix-report.md` complete, with the root cause written **before** the edit.
7. Hardening done: register row if it is a class of mistake, doc healed if one was wrong.

## Not a bug fix

If investigation shows the behaviour is working **as designed** and the *design* is wrong: stop. That is an
enhancement. Say so and switch to [`82-track-b-enhancement.md`](82-track-b-enhancement.md) **with the user's
agreement**.

Do not redesign a feature inside a hotfix. The hotfix has no gap rounds, no design review, and no
`do-not-break.md` — which are exactly the things a design change needs.

## What still goes wrong on Track C

| Risk | Why it survives | What compensates |
|------|-----------------|------------------|
| Non-reproducible defects stay open | the track stops rather than guesses, which is correct | the record of what was tried narrows the next attempt |
| Environment-only defects | local, staging and production differ in ways no local run reveals | reproduce in the closest available build, and state which layers remain between the repro and reality |
| Untestable interactions | test environments cannot see rendered styling or dispatch real device input | a named manual check, honestly labelled as manual |
| The cause is correct but incomplete | one mechanism found, a second contributing one missed | the works/broken matrix contrast check; if the fix only partly resolves the symptom, that is a second cause, not a bad fix |
