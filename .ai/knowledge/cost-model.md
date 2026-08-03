# Cost Model

**Load mode: ON-DEMAND.** Read when planning a session's budget, when a session is running long, or when
deciding what to cut.

Every rule in this framework has a cost. Most of them pay for themselves; a few are cheap insurance against
an expensive failure. This file says where the spend goes and which levers actually move it.

**No token or currency figures appear here.** Rates change, models change, and a fabricated number is worse
than no number (**F5**). What is stable is the *relative* shape of the spend and the *ranking* of the levers.

---

## Where the spend goes

| Phase | Share of a session | Dominant driver | Compressible? |
|-------|-------------------|-----------------|---------------|
| Intake | very small | length of the brief | already minimal |
| **Grounding** | moderate, one-off | **breadth of files read** | **yes, substantially** |
| Gap rounds | moderate, linear in rounds | rounds × document size | yes, via the raw requirement |
| **Design** | large | depth of reasoning + files re-read | partially — thinking does not compress |
| **Review rounds** | **largest avoidable share** | rounds × files re-read per round | **yes, and this is the biggest single win** |
| Task elaboration | moderate | all sections vs next section only | yes, via lazy elaboration |
| Execution | large, mostly unavoidable | code volume + gate runs | phase it; gates are cheap next to rework |
| Close-out | small | docs + index rebuild | batch it |

Two observations that matter more than the table:

- **Rework dominates everything.** A wrong premise caught at review costs a stage. Caught at execution it
  costs the design *and* the implementation. Grounding properly is not a cost centre, it is the cheapest
  insurance available.
- **Gates are cheap.** Running all four gates is a rounding error next to one avoidable debugging session.
  Skipping a gate to save time is never the trade it appears to be.

## The levers, ranked by expected saving

Highest first. Applied in this order, each one reduces the cost of the ones below it.

### 1. Hard round caps

Review rounds are the largest avoidable share, and without a cap they expand to fill whatever budget exists.
Track A 3 / 3 / 2, Track B 2 / 2 / 1, B-lite 1 combined.

**No escape hatch is the point.** A cap with an exception clause is a suggestion, and **F7** will find the
exception every time. At a cap the stage closes and open 🟡/🟢 items become recorded residual risks.

### 2. A properly filled raw requirement

**Every pre-answered question is a gap round avoided**, and a gap round is a full read-write cycle over the
requirement plus a wait for the user.

The blocks that pay the most:

| Block | Saves |
|-------|-------|
| Explicit **out of scope** | the highest-yield single line in the framework — it prevents work rather than reviewing it |
| **Definition of done**, written by the user | removes the freedom to redefine done, which is what produces re-work at closure |
| **Must not break** | seeds the register directly; a blank one produced two wrong claims and four extra rounds |
| **Open questions I already know**, with the user's leaning | collapses gap rounds — the single largest available saving |
| Works/broken matrix (bug fix) | often localises a root cause to one file before any code is read |
| Reference paths already known | avoids searching for context the user already had |

Filling the template is not overhead paid for the agent's benefit. It is the cheapest point in the whole
process to spend five minutes.

### 3. Per-feature context pins

A short steering file, re-pinned at every session start and section boundary, replaces re-reading the same
files. It is the cheapest artefact in the framework and it addresses **F2**, **F4** and **F10** at once.

A pin list that grows past a handful of files has stopped being a working set. Prune pins that are never read.

### 4. Lazy task elaboration

Full plans and test cases only for the section about to run. Later sections carry inventory and design
references until their turn.

Elaborating all sections up front produces detail that the first section's discoveries invalidate. That is
paying twice for the same plan.

### 5. Index-first retrieval, no directory sweeps

A targeted query reads a fraction of what a directory sweep reads, and leaves the context window available
for the actual work. A wide read is expensive twice: once to produce, and again because it crowds out what
you need later (**F4** → **F10**).

### 6. Correct track selection

A bug fix that enters the Track A machinery acquires gap rounds, a design stage and two review stages it does
not need. Track C exists so a defect costs what a defect should cost.

The reverse is worse but rarer: a feature done as a bug fix skips the grounding and the contract register,
and pays for it at execution.

### 7. Reuse the survey instead of re-reading source

`relevant_info.md` exists so the source does not have to be read again at design time. Re-reading source
during design means the survey was either not written properly or not trusted — fix whichever it is.

---

## Declaring and checking a budget

Every raw requirement declares a session budget. It does not have to be a number; it has to be a **boundary**:

| Kind | Example |
|------|---------|
| Stage-bounded | "gap analysis and requirement only, this session" |
| Section-bounded | "S0 and S1" |
| Time-bounded | "two hours" |
| Deliverable-bounded | "the design set, no code" |

**Check against it at every stage boundary.** Running well past it silently is **F12**. The correct move is to
report progress and remaining budget, and propose phasing.

## What to cut when the budget is short

In this order. Each cut is a real reduction in assurance, so each is stated to the user rather than made
quietly.

| # | Cut | Loses | Acceptable when |
|---|-----|-------|-----------------|
| 1 | Optional design artefacts (data reference, operator guide, UX doc) | reference convenience | the feature is small enough to hold in one design doc |
| 2 | The architecture overview on Track B | a cross-surface view | two or fewer surfaces interact |
| 3 | Track B full → **B-lite** | one gap round, separate review rounds | one surface, no schema change, no permission change |
| 4 | Phase the feature | nothing — it defers | phases have a clean boundary and each is independently shippable |
| 5 | Reduce section granularity | independent verification per surface | the surfaces are genuinely coupled |

## What is never cut

Cutting any of these does not save the session, it just moves the cost somewhere less visible.

| Never cut | Because |
|-----------|---------|
| **The four gates** | the cheapest verification available; skipping one trades minutes for a debugging session |
| **Grounding with verified pointers** | a wrong premise invalidates everything downstream (**F1**) |
| **`do-not-break.md` on Track B** | it is the *only* control against the unknown-consumer failure |
| **The written root cause before a bug-fix edit** | without it there is no theory to falsify, only guesses to tweak (**F3**) |
| **Fails-before / passes-after on a regression test** | a test that has never been red proves nothing |
| **Approval for irreversible actions** | unrecoverable, by definition |
| **Real gate output in reports** | the claim without the evidence is not verification |
| **The status table and follow-up list** | the externalised state that makes resumption possible at all |

## Signals that cost is going wrong

| Signal | Diagnosis | Move |
|--------|-----------|------|
| Grounding has taken longer than the design will | breadth out of control | stop, write up what you have, proceed (**F12**) |
| The same file opened a third time | should have been pinned | pin it, prune dead pins (**F4**) |
| A review round produced no findings and named no files | the round was spent producing nothing | that is a finding in itself (**F8**) |
| Wanting an extra round | the cap is doing its job | close the stage (**F7**) |
| Re-reading source during design | the survey is not being used | re-read the survey instead |
| Task elaboration for sections three and four while section one is unbuilt | eager elaboration | defer it |
| No file written and no gate run for a long stretch | planning has replaced progress | commit to the smallest next action (**F7**) |
| Two attempts at the same fix | the theory is wrong, not the parameters | stop, write what they rule out, change approach (**F3**) |

## Why the expensive rules are worth it

The framework's costliest requirements are also its highest-return, and it is worth being explicit about the
trade rather than treating them as ceremony.

| Rule | Costs | Prevents | Verdict |
|------|-------|----------|---------|
| Two independent checks for a negative | one extra check | the single most expensive class of wrong claim | clearly worth it |
| Written root cause before any edit | one document, before the fix | the two-strike loop, and speculative edits to working code | clearly worth it |
| Three verification passes | three focused reads | shipping something coherent and wrong | worth it for artefacts and code; theatre on a one-line change — scale it |
| `path:line` on every claim | a citation per claim | **F1** cascades | worth it; it is also what makes review possible |
| Per-section execution reports | one document per section | resuming from memory, and losing follow-ups | worth it, and it doubles as the resume point |
| Cutover with per-item approval | a separate stage | the "impact-free" removal that was not | worth it — the failure it prevents is unrecoverable |
