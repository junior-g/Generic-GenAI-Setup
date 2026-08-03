# Track A — New Feature

**Load mode: ON-DEMAND.** Pull in when the work is a capability that does not exist yet: new surfaces, new
data, possibly a new actor. If live data and existing contracts are the main constraint, it is
[Track B](82-track-b-enhancement.md) instead.

Templates for every output: [`../templates/README.md`](../templates/README.md).
Paths from `config.yml` → `paths`. `<root>` below means `<artefact_root>/<slug>`.

---

## Round caps — hard, no escape hatch

| Stage | Cap |
|-------|-----|
| Gap analysis | **3 rounds** |
| Design review | **3 rounds** |
| Tasks review | **2 rounds** |

At a cap the stage closes. Open 🟡/🟢 items become residual risks in the final review report. An open 🔴
**blocks** the stage — report it and stop; it does not buy another round.

Wanting another round is **F7**. Playbook: [`84-failure-playbooks.md`](84-failure-playbooks.md).

---

## Stage 0 — frame

### `A0` — Intake

Create `<root>/` and place the filled raw requirement
([`raw-requirement-new-feature.md`](../templates/raw-requirement-new-feature.md)) at
`<root>/<Feature-Name>.md`.

Restate: work type · actors · surfaces · irreversible surfaces · phasing · **session budget**. If the user's
brief left a section blank, say which — a blank "must not break" section is the single highest-risk gap in
intake.

### `A1` — Grounding → `<root>/relevant_info.md`

Template: [`relevant-info.md`](../templates/relevant-info.md).

1. Check the retrieval tier is usable (`config.yml` → `retrieval`); build or refresh it if stale.
2. Five to ten **targeted** queries, derived from the nouns in the brief. Never a directory-wide read
   (**F4**).
3. Every claim carries a `path:line` pointer **and is verified by reading the file**.
4. Negative claims — "X does not exist" — need **two independent checks by different methods**, recorded in
   the negative-findings table with both methods named.

This step is where cost is either controlled or lost. Reading broadly here feels thorough and produces a
context window with no room left for the actual work.

### `A2` — Context anchor → `<rules_root>/8x-<slug>.md`

Template: [`feature-steering.md`](../templates/feature-steering.md). Carries AIM · scope · out-of-scope ·
pinned context files · must-not-break summary.

**Pull it in at the start of every session from here on**, and at every section boundary. It is the
anti-drift anchor (**F2**) and the context budget (**F4**) in one artefact, and it is the cheapest thing in
the whole track.

## Stage 1 — settle intent

### `A3` — Gap analysis → `<root>/all_gaps.md`, max 3 rounds

Template: [`all-gaps.md`](../templates/all-gaps.md).

Per gap: observation · best-practice recommendation · the question · a `Your answer:` slot · severity 🔴🟡🟢.
Group by theme, not by file.

Round N+1 hunts **only** gaps opened by the user's answers. It never re-treads settled ground — re-asking a
settled question is how three rounds become six.

**STOP after each round.** Wait for answers.

### `A4` — Settled requirement → `<root>/final_requirement.md`

Template: [`final-requirement.md`](../templates/final-requirement.md).

`R1`…`Rn` in testable form, each with acceptance criteria · error and edge behaviour · data model with
entity diagram, state machines and indexes · API surface · route/screen surface · flows · non-functional
requirements · phasing · definition of done.

Gaps still open at the cap appear **inline as a multiple choice with a reversible default chosen**, so the
work can proceed without pretending the question is settled.

**STOP for user review.** After approval, changes are **amendments in the amendment log**, never silent
edits. A silently edited requirement makes every review that cited it wrong (**F1**).

## Stage 2 — design

### `A5` — Design → `<root>/design/`

`00-architecture-overview.md`
([template](../templates/design-00-architecture-overview.md)) plus one
`design-0N-<topic>.md` per section ([template](../templates/design-section.md)).

Section order inside each design doc is deliberate and not to be rearranged: **surfaces → components →
reuse decision → boundary split → validation**. Reuse is decided *after* components are enumerated, because
deciding it first produces a design shaped by what already exists rather than by what is needed.

Mandatory in every design:

- **Runtime feasibility, stated explicitly.** Can the code in this layer actually reach that resource, in
  that environment, with that latency and that permission? Assumed feasibility is the most expensive class
  of design defect, because it survives until execution.
- **Reversibility ledger** — every action classified pure / reversible / compensable / irreversible.
- **Audit fields** on every new entity: created-by, created-at, updated-by, updated-at, or the project's
  declared equivalent.
- Diagrams as text (Mermaid or equivalent), so they diff and survive.

Optional supporting artefacts, when the feature is large enough to earn them:

| Artefact | When |
|----------|------|
| `design/DOC-1-data-reference.md` | more than about four entities — the full schema reference in one place |
| `design/DOC-2-<x>-guide.md` | a repeated operator procedure exists |
| `design/design-ux.md` | new user-facing surfaces — states, spacing, responsive and empty/error treatments |
| `design/design-icons.md` | new iconography — source, analysis, variations, chosen design, rationale |

### `A6` — Design review → `<root>/design/design-review-round-N.md`, max 3

Template: [`review-round.md`](../templates/review-round.md), design mode.

Each round **reads the real source files it questions** and either emits numbered `DR-n` findings or signs
off **naming every file read**. "Looks good" is not a review outcome — a round that produces nothing has
still cost a round (**F8**).

At least one round includes the security checklist from [`50-security.md`](50-security.md).

Fold findings back into the design *and* the requirement, so the two do not diverge. **STOP.**

## Stage 3 — plan

### `A7` — Task plan → `<root>/tasks/`

`README.md` ([template](../templates/tasks-readme.md)): sections `S0`…`Sn` · dependency diagram · global
rules · **status table**.
`task_details.md` ([template](../templates/task-details.md)): the authoritative inventory.

`S0` is always types, schema and the data layer. Everything else depends on it, and discovering that after
building three surfaces costs a re-plan.

**Full plans and test cases only for the section about to run.** Later sections carry inventory and design
references until their turn. Elaborating all sections up front is planning that will be invalidated (**F7**).

Each section's plan uses the matching discipline format from
[`../templates/task-formats/README.md`](../templates/task-formats/README.md).

### `A8` — Tasks review → `<root>/tasks/tasks-review-round-N.md`, max 2

Template: [`review-round.md`](../templates/review-round.md), tasks mode. Read the actual components each task
touches.

Then **consolidate**: requirement, design and tasks must state the same reality. Write
`<root>/final-review-report.md` ([template](../templates/final-review-report.md)) with the traceability
matrix, the orphan check in both directions, consistency checks, compliance, reversibility summary, residual
risks, and a verdict. **STOP.**

## Stage 4 — execute, per section

### `A9` — Implement

1. **Re-pin** `8x-<slug>.md`, the relevant `final_requirement.md` `R`-block, and that section's design doc.
   Every section, not just the first (**F2**, **F10**).
2. Implement in dependency order.
3. Extract pure logic so it is testable without a database, a network, or a UI.
4. Run **all four gates**.

### `A10` — Report → `<root>/tasks/execution-report-S<n>.md`

Template: [`execution-report.md`](../templates/execution-report.md). Task outcomes · deviations from design
with the documents updated · **gate evidence, real output** · verification per requirement · follow-ups ·
discoveries.

Update the status table in `tasks/README.md`. **A section is not complete while it has an open follow-up.**
Burn them down before the next section starts (**F9**).

Removals discovered along the way are **recorded and deferred**, never executed inside a feature section.

### `A11` — Cutover, separate approval → `<root>/tasks/cutover-report.md`

Template: [`cutover-report.md`](../templates/cutover-report.md). Only after every feature section is green.
**Removals and retirements only.**

Per item: pre-flight checklist · consumers found **and read** · an unscoped search, not just a scoped one ·
reversal path · **your individual approval** · gates after each item.

## Stage 5 — close out

### `A12` — Close

- `<root>/tasks/manual-testing.md` executed ([template](../templates/manual-testing.md)).
- Project documentation synced for everything this feature changed.
- Screenshots or captures for new user-facing surfaces, if the project keeps them.
- Retrieval index rebuilt, if tier 2.
- [`../INDEX.md`](../INDEX.md): Zone 2 active-work row moved to shipped.
- **Delete `<rules_root>/8x-<slug>.md`.** The feature has shipped; `<root>/**` is the lasting record.
  Leaving feature files to accumulate in the rule set recreates the instruction-surface problem the whole
  framework exists to avoid.

If a feature genuinely warrants permanent steering — a durable subsystem rather than a project — say so
explicitly at closure and **promote** the content into a numbered project rule instead of quietly leaving
the feature file behind.

---

## Definition of done

1. Every requirement's acceptance criteria **observably** met.
2. All four gates green, with real output shown.
3. New pure logic unit-tested; new boundaries integration-tested.
4. Locale parity for user-facing strings, if the project has multiple locales.
5. Nothing from [`70-do-not-regress.md`](70-do-not-regress.md) reintroduced.
6. 3× verification complete on the artefact set — [`62-3x-verification.md`](62-3x-verification.md).
7. Docs synced, `INDEX.md` current, index rebuilt, feature steering file retired.

## What still goes wrong on Track A

Honest list. The track reduces these; it does not eliminate them.

| Risk | Why it survives | What compensates |
|------|-----------------|------------------|
| Requirement churn after design begins | a late `R`-number change invalidates design and tasks | design starts only after approval; later changes are logged amendments with an impact note |
| A section turns out to depend on a later one | dependency diagrams are inferred, not proven | surfaces at execution; costs a re-plan of two sections |
| A capped review round misses a blocker | caps are hard, so a late finding cannot buy a round | front-loaded grounding, the runtime-feasibility check, and a 🔴 blocking the stage rather than being absorbed |
| Cost concentrates in design | thinking is not compressible | phase harder; smaller sections |
| A claim is read *and misread* | reading the right file and understanding it are different skills | require a quoted line, not a paraphrase, for any 🔴 claim |
