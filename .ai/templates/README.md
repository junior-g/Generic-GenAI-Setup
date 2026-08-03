# Artefact Templates

One template for every output of every step of every track.

**How to use one:** copy it to the output path, delete its HTML-comment header block, fill it in.

**The rule for all of them:** *sections are not deleted.* Write `None` or `Not applicable`, because an empty
section and a forgotten section look identical — and that ambiguity is exactly where a wrong assumption
survives review.

Tracks: [`../rules/80-work-intake.md`](../rules/80-work-intake.md) →
[`81`](../rules/81-track-a-new-feature.md) · [`82`](../rules/82-track-b-enhancement.md) ·
[`83`](../rules/83-track-c-bug-fix.md). Paths come from `config.yml` → `paths`; `<root>` below means
`<artefact_root>/<slug>`.

---

## Step → template map

### Track A — new feature

| Step | Output path | Template |
|------|-------------|----------|
| `A0` | `<root>/<Feature-Name>.md` | [`raw-requirement-new-feature.md`](raw-requirement-new-feature.md) |
| `A1` | `<root>/relevant_info.md` | [`relevant-info.md`](relevant-info.md) |
| `A2` | `<rules_root>/8x-<slug>.md` | [`feature-steering.md`](feature-steering.md) |
| `A3` | `<root>/all_gaps.md` | [`all-gaps.md`](all-gaps.md) |
| `A4` | `<root>/final_requirement.md` | [`final-requirement.md`](final-requirement.md) |
| `A5` | `<root>/design/00-architecture-overview.md` | [`design-00-architecture-overview.md`](design-00-architecture-overview.md) |
| `A5` | `<root>/design/design-0N-<topic>.md` | [`design-section.md`](design-section.md) |
| `A6` | `<root>/design/design-review-round-N.md` | [`review-round.md`](review-round.md) — design mode |
| `A7` | `<root>/tasks/README.md` | [`tasks-readme.md`](tasks-readme.md) |
| `A7` | `<root>/tasks/task_details.md` | [`task-details.md`](task-details.md) |
| `A8` | `<root>/tasks/tasks-review-round-N.md` | [`review-round.md`](review-round.md) — tasks mode |
| `A8` | `<root>/final-review-report.md` | [`final-review-report.md`](final-review-report.md) |
| `A9`–`A10` | `<root>/tasks/execution-report-S<n>.md` | [`execution-report.md`](execution-report.md) |
| `A11` | `<root>/tasks/cutover-report.md` | [`cutover-report.md`](cutover-report.md) |
| `A12` | `<root>/tasks/manual-testing.md` | [`manual-testing.md`](manual-testing.md) |

### Track B — enhancement

Same set, plus one mandatory artefact and a different intake format.

| Step | Output path | Template |
|------|-------------|----------|
| `B0` | `<root>/<Enhancement>.md` | [`raw-requirement-enhancement.md`](raw-requirement-enhancement.md) |
| `B1` | `<root>/relevant_info.md` | [`relevant-info.md`](relevant-info.md) |
| `B1` | `<root>/do-not-break.md` **(mandatory)** | [`do-not-break.md`](do-not-break.md) |
| `B2` | `<rules_root>/8x-<slug>.md` | [`feature-steering.md`](feature-steering.md) |
| `B3` | `<root>/all_gaps.md` | [`all-gaps.md`](all-gaps.md) |
| `B4` | `<root>/final_requirement.md` | [`final-requirement.md`](final-requirement.md) |
| `B5` | `<root>/design/design-0N-<topic>.md` | [`design-section.md`](design-section.md) |
| `B5` | `<root>/design/00-architecture-overview.md` *(only if >2 surfaces interact)* | [`design-00-architecture-overview.md`](design-00-architecture-overview.md) |
| `B6` | `<root>/design/design-review-round-N.md` | [`review-round.md`](review-round.md) — design + regression sweep |
| `B7` | `<root>/tasks/README.md`, `task_details.md` | [`tasks-readme.md`](tasks-readme.md), [`task-details.md`](task-details.md) |
| `B8` | `<root>/tasks/tasks-review-round-1.md` | [`review-round.md`](review-round.md) — tasks mode |
| `B8` | `<root>/final-review-report.md` | [`final-review-report.md`](final-review-report.md) |
| `B9` | `<root>/tasks/execution-report-S<n>.md` | [`execution-report.md`](execution-report.md) |
| `B10` | `<root>/tasks/cutover-report.md` | [`cutover-report.md`](cutover-report.md) |
| `B11` | `<root>/tasks/manual-testing.md` | [`manual-testing.md`](manual-testing.md) |

**Track B-lite** — one surface, no schema change, no permission change. Uses: raw requirement ·
`relevant_info.md` · `do-not-break.md` (**still mandatory**) · `all_gaps.md` (1 round) ·
`final_requirement.md` · one design doc · **one combined** review round · one execution report.

### Track C — bug fix

| Step | Output path | Template |
|------|-------------|----------|
| `C0` | `<artefact_root>/hotfixes/<slug>/README.md` | [`raw-requirement-bug-fix.md`](raw-requirement-bug-fix.md) |
| `C2`–`C6` | `<artefact_root>/hotfixes/<slug>/fix-report.md` | [`fix-report.md`](fix-report.md) |

The fix report is **started at `C2`** — its root-cause section is filled before any code edit. The report
existing before the fix is what enforces the gate; it cannot be satisfied retroactively.

### Track T — trivial

No artefacts. State the change, make it, run the gates, report in one line.

---

## Canonical output names

Fixed, so cross-references and retrieval keep working across projects.

| Artefact | Filename |
|----------|----------|
| Raw brief | `<Feature-Name>.md` in the work folder |
| Existing-project survey | `relevant_info.md` |
| Preserved contracts | `do-not-break.md` |
| Gap analysis | `all_gaps.md` |
| Settled requirement | `final_requirement.md` |
| Architecture overview | `design/00-architecture-overview.md` |
| Section design | `design/design-0N-<topic>.md` |
| Design review | `design/design-review-round-N.md` |
| Task map | `tasks/README.md` |
| Task inventory | `tasks/task_details.md` |
| Tasks review | `tasks/tasks-review-round-N.md` |
| Final review | `final-review-report.md` |
| Execution | `tasks/execution-report-S<n>.md` |
| Cutover | `tasks/cutover-report.md` |
| Manual QA | `tasks/manual-testing.md` |
| Bug report | `hotfixes/<slug>/README.md` |
| Fix report | `hotfixes/<slug>/fix-report.md` |
| Feature steering | `<rules_root>/8x-<slug>.md` — **deleted at close-out** |

## Task-type formats

Within a track, each execution section has a discipline. Its task plan uses the matching format from
[`task-formats/README.md`](task-formats/README.md) — UI, backend, database, infrastructure, data, or
integration — which layers discipline-specific checks on top of the common task blocks.

---

## Why the templates are shaped this way

Every recurring block closes a specific failure mode. Codes are the `F1`–`F13` vocabulary from
[`../rules/05-failure-detection.md`](../rules/05-failure-detection.md); detail in
[`../knowledge/failure-taxonomy.md`](../knowledge/failure-taxonomy.md).

| Recurring block | Appears in | Closes |
|-----------------|-----------|--------|
| `path:line` pointer + a read-and-verified column | survey, contracts, tasks, review | **F5** — a search hit is a pointer, never evidence |
| **Negative findings** table requiring two independent checks | survey | **F5** — the two most expensive wrong claims in this framework's history were both negatives |
| Explicit out-of-scope list | raw requirement, requirement, design section | **F7** over-planning, **F2** scope creep |
| **Definition of done written by the user** | raw requirement, requirement | **F9** — removes the freedom to redefine done |
| Round caps stated in the header, no escape hatch | gaps, review round | **F7**, **F12** |
| "Findings, or a sign-off naming the files read" | review round | **F8** — "looks good" becomes unavailable |
| Status table as the declared resume point | task map, execution report | **F9** amnesia, **F2** drift |
| Follow-ups block section closure | execution report | **F9** |
| Reversibility ledger + separate cutover approval | architecture overview, cutover | **F6** |
| Two-strike attempt log | fix report | **F3** |
| Root cause required before any edit | fix report | **F5**, **F1** |
| Fails-before / passes-after evidence | fix report | verification theatre |
| Amendment log instead of silent edits | requirement | **F1**, **F11** |
| Pinned context file list | feature steering | **F4**, **F12** |
| Objective restated at every load | feature steering, requirement | **F2**, **F10** |
| Corrections log | survey, review round | **F11** — makes the failure pattern visible instead of burying it |
| Real gate output, not a claim | execution report, fix report | **F8**, **F9** |
| Ripple-effects table per task | task details | **F1** cascading |
| Contracts-preserved table per section | execution report | **F5** on Track B |

## What a filled artefact must never contain

| Never | Instead |
|-------|---------|
| A deleted section | `None` or `Not applicable` |
| `TODO`, `FIXME`, `tbd`, a placeholder value | a named open question with the reversible default in use |
| A claim with no pointer | `path:line`, read and verified |
| "Gates pass" | the real output |
| "Looks good" as a review outcome | findings, or a sign-off naming every file read |
| An invented figure | a measured one, or nothing |
| A paraphrase standing in for a 🔴 claim | the quoted line |
