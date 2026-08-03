<!--
TEMPLATE — review round. Two modes.
  design mode → <artefact_root>/<slug>/design/design-review-round-N.md   (step A6 / B6)
  tasks mode  → <artefact_root>/<slug>/tasks/tasks-review-round-N.md     (step A8 / B8)
Copy, delete this header, set the mode in the header table.

CAPS ARE HARD, NO ESCAPE HATCH. Track A: 3 design / 2 tasks. Track B: 2 design / 1 tasks. B-lite: 1 combined.

A ROUND MUST EMIT NUMBERED FINDINGS, OR AN EXPLICIT SIGN-OFF NAMING EVERY FILE READ. "Looks good" is not a
review outcome — it consumes a round and provides no assurance (F8). A round that produces nothing is itself
a finding about the review.

EACH ROUND READS THE REAL SOURCE FILES IT QUESTIONS. Reviewing the design against the design only checks
internal consistency, which is the cheapest and least valuable thing a review can do.

TRACK B: one of the design rounds MUST include the regression sweep (§5).

STOP at the end of each round.

Sections are not deleted. Write None or Not applicable.
-->

# Review Round <n> — <slug>

| | |
|---|---|
| Mode | **design** / **tasks** |
| Round | <n> of <cap> — **hard cap, no escape hatch** |
| Reviewing | <the documents under review> |
| Against | `../final_requirement.md` v<n> <, `../do-not-break.md`> |
| Regression sweep included | yes / no *(mandatory in one Track B design round)* |
| Outcome | **findings** / **sign-off** |

## 1. Files read this round

**The evidence that this was a review and not a re-read of the design.** A round with no source files here
did not review anything.

| File | Why read | What it confirmed or contradicted |
|------|----------|----------------------------------|
| `path:line` | | |

## 2. Findings

Numbered `DR-<n>` continuing across rounds — `DR-1` in round 1, and round 2 starts where round 1 stopped, so
a finding is referenceable for the life of the work.

### DR-<n> — <short title> 🔴

| | |
|---|---|
| **Severity** | 🔴 blocks / 🟡 should fix / 🟢 nice to have |
| **Where** | `path:line` or the document section |
| **Category** | correctness / feasibility / security / contract / traceability / consistency / scope |

**Observation.** <What the source actually shows, cited. Fact, not opinion.>

**Why it is a finding.** <What breaks, or what is undetermined.>

**Recommendation.** <The specific change.>

**Resolution.** <Filled when folded in: what changed, in which document.>

---

### DR-<n+1> — <short title> 🟡

<Same structure.>

---

## 3. Requirement coverage

| Requirement | Covered by | Adequately? | Finding |
|-------------|-----------|-------------|---------|
| R1 | `design-01.md` §4 | ✅ | |

| | |
|---|---|
| Requirements with no design coverage | <list, or none> |
| Design content tracing to no requirement | <list, or none — this is scope creep, **F2**> |

## 4. Feasibility re-check

Every boundary crossing the design assumes, checked against the real code rather than the design's claim.

| Assumption | Verified how | Holds? |
|------------|-------------|--------|
| | `path:line` | ✅ / ❌ |

## 5. Regression sweep

**Mandatory in one Track B design round.** For every symbol, route, field, key, or component being changed:
find the consumers and **read them**.

| Thing changing | Consumers found | Read? | Impact | Already in `do-not-break.md`? |
|----------------|-----------------|-------|--------|------------------------------|
| | `path:line` | ✅ | | yes / **appended now** |

| | |
|---|---|
| Search method used | <and confirmation it was unscoped, not just scoped> |
| Consumers found that were **not** in `do-not-break.md` | <count — appended> |
| Any consumer that could not be located | <and what that means> |

**The register is not frozen after `B1`.** Everything found here is appended to it.

## 6. Security re-check

| # | Check | Result | Evidence |
|---|-------|--------|----------|
| 1 | New inputs validated | | |
| 2 | New data access authorised, ownership-scoped | | |
| 3 | No secret in source, output, or logs | | |
| 4 | No interpolated query, command, or path | | |
| 5 | New exposed surface has auth, or the gap is stated | | |
| 6 | Errors leak nothing | | |
| 7 | Nothing irreversible without approval | | |

## 7. Tasks-mode checks

Only in tasks mode; `Not applicable` in design mode.

| Check | Result |
|-------|--------|
| Every task traces to a requirement | |
| Every requirement has at least one task | |
| Dependency order is correct — nothing depends on a later section | |
| `S0` covers types, schema and the data layer | |
| Non-destructive ordering: new path before old path is touched | |
| A dedicated regression section exists *(Track B)* | |
| Each task's test plan asserts its own acceptance criteria | |
| Ripple effects identified per task | |
| Only the next section is fully elaborated | |
| Removals are deferred to cutover, not inline | |

## 8. Corrections to earlier claims

Where this round found something previously stated to be wrong. **Non-empty is a good outcome.**

| Claim | Stated in | Actually | Evidence | Artefacts to re-walk |
|-------|-----------|----------|----------|---------------------|
| | `relevant_info.md` §2 row 4 | | `path:line` | requirement R<n>, design 02 |

A correction here is **F1**: fix it at the source, then walk forward through everything that cited it. Also
append it to the survey's corrections log.

## 9. Round outcome

Exactly one of the following.

### Findings

| Finding | Severity | Folded into | Done |
|---------|----------|-------------|------|
| DR-<n> | | | ☐ |

### Sign-off

> No findings. Verified by reading: `path`, `path`, `path`. Specifically checked: <what>, <what>, <what>.

A sign-off without the file list is not a sign-off.

## 10. Cap status

| | |
|---|---|
| Rounds used | <n> of <cap> |
| Rounds remaining | <n> |
| At cap? | yes / no |

### If at cap

| Open finding | Severity | Disposition |
|--------------|----------|-------------|
| DR-<n> | 🟡 / 🟢 | residual risk in `final-review-report.md` |
| DR-<n> | 🔴 | **blocks the stage — reported, stage stops** |

At the cap the stage closes. 🟡 and 🟢 become recorded residual risks. **A 🔴 blocks the stage and is
reported** — it does not buy another round, and it is not downgraded to let the work continue.

**STOP.**
