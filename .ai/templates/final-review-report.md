<!--
TEMPLATE — final review. Copy to <artefact_root>/<slug>/final-review-report.md, delete this header.
Written at step A8 / B8, after the tasks review, BEFORE execution begins.

PURPOSE: prove that requirement, design and tasks state the SAME reality, and that nothing is orphaned in
either direction. This is the last cheap moment to find an inconsistency — after this, finding one costs code.

THE ORPHAN CHECK RUNS BOTH WAYS. A requirement with no task is a gap. A task with no requirement is scope
creep (F2). Both matter, and only checking one direction is the common mistake.

THIS IS ALSO WHERE RESIDUAL RISKS ARE RECORDED. Everything left open by a hard cap lands here, named, with
the default applied — that is what makes a cap honest rather than a way of hiding open questions.

Sections are not deleted. Write None or Not applicable.
-->

# Final Review Report — <slug>

| | |
|---|---|
| Track | A / B / B-lite |
| Requirement | `final_requirement.md` v<n>, approved <date> |
| Design | <n> documents |
| Tasks | <n> sections, <n> tasks |
| Design rounds used | <n> of <cap> |
| Tasks rounds used | <n> of <cap> |
| **Verdict** | **ready to execute** / not ready |

## 1. Traceability matrix

Every requirement, forward through design to task to verification.

| Requirement | Design | Section | Tasks | Test level | Verified in |
|-------------|--------|---------|-------|-----------|-------------|
| R1 | `design-01` §4 | S1 | T1.1, T1.2 | unit + integration | `execution-report-S1.md` |

## 2. Orphan check — both directions

### Requirements with no task

| Requirement | Why | Action |
|-------------|-----|--------|
| | | task added / deliberately deferred / withdrawn |

Empty is the expected result. A non-empty row that is not deliberate blocks execution.

### Tasks tracing to no requirement

| Task | What it does | Action |
|------|--------------|--------|
| | | removed / requirement amended to cover it |

**This is scope creep (F2).** Either the requirement gains an amendment or the task goes. It does not simply
stay.

### Design content tracing to no requirement

| Design | Section | Action |
|--------|---------|--------|
| | | |

## 3. Consistency across artefacts

The same fact stated in three places must be stated the same way. A disagreement here is **F11**.

| Fact | Requirement says | Design says | Tasks say | Agree? |
|------|-----------------|-------------|-----------|--------|
| | | | | ✅ / ❌ |

Check specifically: field names and types · entity names · status and state values · endpoint paths and verbs ·
actor and role names · error and status codes · terminology from the product vocabulary.

| | |
|---|---|
| Disagreements found | <n> |
| All resolved? | ☐ — an unresolved one blocks execution |

## 4. Compliance

| # | Check | Result | Evidence |
|---|-------|--------|----------|
| 1 | Every project convention followed in the plan | | |
| 2 | Nothing on the do-not-regress register reintroduced | | |
| 3 | Security checklist passed at design review | | |
| 4 | Audit fields on every new entity | | |
| 5 | Validation at every new boundary | | |
| 6 | Authorisation at every new data access | | |
| 7 | Locale parity planned, if applicable | | |
| 8 | Accessibility floor planned, if there is UI | | |
| 9 | New dependencies pinned and justified | | |
| 10 | No secret in any artefact | | |

## 5. Reversibility summary

Consolidated from every design's ledger.

| Class | Count | Items |
|-------|-------|-------|
| Pure | | |
| Reversible | | |
| Compensable | | |
| **Irreversible** | | |

### Irreversible actions

| # | Action | Approval | Cutover row | Reversal path |
|---|--------|----------|-------------|---------------|
| 1 | | ☐ | | |

**Every irreversible action must have an approval box and a cutover row before execution begins.** One
without both blocks execution.

### Non-idempotent operations

| Operation | Consequence of a blind retry | Guard |
|-----------|------------------------------|-------|
| | | |

## 6. Contract preservation plan

**Track B.** How every register row will be verified.

| | |
|---|---|
| Rows in `do-not-break.md` | <n> |
| Rows with a named check | <n> |
| Rows with no check yet | **<n> — must be 0** |
| Regression section | S<n> |
| Rows deliberately broken, approved | <n> |

## 7. Migration plan review

Only when live data exists.

| # | Check | Result |
|---|-------|--------|
| 1 | Records predating the change have a defined read behaviour | |
| 2 | Rollout read path and write path both defined | |
| 3 | Backfill is idempotent, batched, resumable | |
| 4 | Reversal path defined and plausible | |
| 5 | Expand-migrate-contract ordering, not contract-first | |
| 6 | What cannot be verified locally is named | |

## 8. Test coverage plan

| Requirement | Unit | Integration | Manual | Gap |
|-------------|------|-------------|--------|-----|
| R1 | ✅ | ✅ | — | |

| | |
|---|---|
| Requirements with no automated coverage | <and why> |
| Manual-only checks | <listed in `manual-testing.md`> |

## 9. Residual risks

**Everything left open. This section is what makes a hard cap honest.**

| # | Risk | Source | Severity | Default applied | Reversible? | Impact if wrong |
|---|------|--------|----------|-----------------|-------------|-----------------|
| 1 | | gap G<n>.<n> unanswered at cap | 🟡 | | yes | |
| 2 | | design finding DR-<n> open at cap | 🟢 | | | |
| 3 | | cannot be verified locally | | | | |

### 🔴 open at a cap

| Risk | Why it blocks | Reported |
|------|---------------|----------|
| | | ☐ |

**A 🔴 here means the stage did not close cleanly.** It is reported to the user and execution does not begin
on it. It is not downgraded to 🟡 to unblock the work.

## 10. Round usage

| Stage | Cap | Used | Closed because |
|-------|-----|------|----------------|
| Gap analysis | | | all settled / cap reached |
| Design review | | | |
| Tasks review | | | |

## 11. 3× verification of the artefact set

Run over the whole set, not one file.

| Pass | Checked | Findings | Fixed |
|------|---------|----------|-------|
| P1 logic | every claim cited and read; negatives double-checked | | |
| P2 structure | links resolve; no orphans; numbering intact; traceability both ways; no placeholders | | |
| P3 instruction | every requirement addressed; nothing extra; out-of-scope respected; caps honoured | | |

## 12. Verdict

| | |
|---|---|
| **Ready to execute?** | **yes / no** |
| Blocking items | |
| First section | S0 |
| Conditions on proceeding | |

### Sign-off

> Reviewed the full artefact set. Files read this round: `path`, `path`, `path`.
> Traceability complete both directions. <n> residual risks recorded. <n> irreversible actions routed to
> cutover with approval boxes. Verdict: <verdict>.

**STOP — execution begins after this is approved.**
