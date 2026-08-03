# Central Index

The one file that knows where everything is. Two zones:

- **Zone 1 — payload.** The framework's own files. Identical in every installation. Do not edit rows here
  when you install; they are the same everywhere.
- **Zone 2 — project.** Generated at activation step `S7` and maintained continuously by the auto-healing
  rule. Everything specific to the project you installed into.

> **This file is the auto-healing target.** Whenever a document is created, changed, superseded or found
> to be wrong, the corresponding row here is updated **in the same turn**. Rule:
> [`rules/65-auto-healing-docs.md`](rules/65-auto-healing-docs.md). An index that lags reality is worse
> than no index, because it is trusted.

| | |
|---|---|
| Framework version | 1.1.0 |
| Last payload change | see [`../docs/CHANGELOG.md`](../docs/CHANGELOG.md) |
| Zone 2 status | not generated — this is the pristine framework copy, not an installation |

---

## Zone 1 — payload

### Anchors and contract

| File | Load mode | Purpose |
|------|-----------|---------|
| [`../MAIN.md`](../MAIN.md) | ALWAYS | Activation anchor. Trigger phrase, `S0`–`S9` summary, the eleven non-negotiables |
| [`manifest.md`](manifest.md) | ALWAYS | Load-mode contract: which rules load when, the fallbacks, the 14 behaviour checks |
| [`config.example.yml`](config.example.yml) | ON-DEMAND | Annotated shape of the per-project `config.yml` written at `S4` |
| [`../README.md`](../README.md) | ON-DEMAND | Human-facing overview and quickstart |
| [`../docs/rationale.md`](../docs/rationale.md) | ON-DEMAND | Why each rule exists, the failure it prevents, and what the framework does **not** fix |
| [`../docs/CHANGELOG.md`](../docs/CHANGELOG.md) | ON-DEMAND | Framework version history and re-sync notes |

### Universal rules — copied verbatim, never edited per project

| File | Load mode | Covers |
|------|-----------|--------|
| [`rules/00-agent-contract.md`](rules/00-agent-contract.md) | ALWAYS | The operating contract: grounding, evidence, reversibility, honesty, scope, reporting |
| [`rules/01-session-preflight.md`](rules/01-session-preflight.md) | ALWAYS | The one enforcement moment: no file edit before the preflight block — track, stage, gates, retrieval, pins |
| [`rules/05-failure-detection.md`](rules/05-failure-detection.md) | ALWAYS | Runtime self-check. Signal → `F1`–`F13` code → first move. The hard-stop list |
| [`rules/50-security.md`](rules/50-security.md) | ALWAYS | Secrets, input validation, authz, untrusted input, dependencies, PII, new endpoints |
| [`rules/60-workflow-and-verification.md`](rules/60-workflow-and-verification.md) | ALWAYS | Where work starts, the grounding rule, the four gates, irreversible actions, resuming |
| [`rules/62-3x-verification.md`](rules/62-3x-verification.md) | ALWAYS | The three verification passes and the discard-and-regenerate rule |
| [`rules/65-auto-healing-docs.md`](rules/65-auto-healing-docs.md) | ALWAYS | Pause-fix-index-resume protocol for missing, incomplete or stale docs |
| [`rules/70-do-not-regress.md`](rules/70-do-not-regress.md) | ALWAYS | Must-not-reintroduce register. Universal seed rows + project rows appended at `S5` |
| [`rules/95-retrieval.md`](rules/95-retrieval.md) | ALWAYS | How to find things. Retrieval tiers, index-first protocol, when to skip it |
| [`rules/80-work-intake.md`](rules/80-work-intake.md) | AUTO | Track classification A / B / C / T, triage questions, rules binding every track |
| [`rules/81-track-a-new-feature.md`](rules/81-track-a-new-feature.md) | ON-DEMAND | Track A steps `A0`–`A12`, caps 3 / 3 / 2, definition of done |
| [`rules/82-track-b-enhancement.md`](rules/82-track-b-enhancement.md) | ON-DEMAND | Track B steps `B0`–`B11`, caps 2 / 2 / 1, `do-not-break` register, regression sweep |
| [`rules/83-track-c-bug-fix.md`](rules/83-track-c-bug-fix.md) | ON-DEMAND | Track C steps `C0`–`C7`, root-cause gate, two-strike circuit breaker |
| [`rules/84-failure-playbooks.md`](rules/84-failure-playbooks.md) | ON-DEMAND | Per-`F<n>` containment / correction / prevention, look-alike disambiguation |

### Project rules — generated at `S5`, one per project

| Template | Generates | Load mode | Covers |
|----------|-----------|-----------|--------|
| [`rules/project/README.md`](rules/project/README.md) | — | ON-DEMAND | How the project layer is generated and kept honest |
| [`rules/project/product.template.md`](rules/project/product.template.md) | `rules/project/product.md` | ALWAYS | What the product is, who uses it, the domain vocabulary, the brand constants |
| [`rules/project/structure.template.md`](rules/project/structure.template.md) | `rules/project/structure.md` | ALWAYS | Directory layout, placement rules for new code, module boundaries |
| [`rules/project/tech.template.md`](rules/project/tech.template.md) | `rules/project/tech.md` | ALWAYS | Stack and versions, the four gate commands, config and env access, version-specific traps |
| [`rules/project/10-code-conventions.template.md`](rules/project/10-code-conventions.template.md) | `rules/project/10-code-conventions.md` | ALWAYS | Imports, naming, error handling, typing, forbidden patterns |
| [`rules/project/15-design-system.template.md`](rules/project/15-design-system.template.md) | `rules/project/15-design-system.md` | FILE-MATCH (UI paths) | Design tokens, component model, accessibility floor, how UI styling is validated |
| [`rules/project/20-api-standards.template.md`](rules/project/20-api-standards.template.md) | `rules/project/20-api-standards.md` | FILE-MATCH (API paths) | Handler shape, validation, status codes, versioning, error envelope |
| [`rules/project/30-data-and-types.template.md`](rules/project/30-data-and-types.template.md) | `rules/project/30-data-and-types.md` | FILE-MATCH (model paths) | Stores, schema and migration policy, type placement, audit fields |
| [`rules/project/90-docs-map.template.md`](rules/project/90-docs-map.template.md) | `rules/project/90-docs-map.md` | ON-DEMAND | Curated reading order over the project's own documentation |

### Knowledge

| File | Load mode | Purpose |
|------|-----------|---------|
| [`knowledge/ai_troubleshooting.md`](knowledge/ai_troubleshooting.md) | ON-DEMAND | AI limitations and hard remedies: hallucination, context loss, code-generation loops, and the guardrails against each |
| [`knowledge/failure-taxonomy.md`](knowledge/failure-taxonomy.md) | ON-DEMAND | `F1`–`F13` reference: definition, observable signature, diagnostic, severity class |
| [`knowledge/cost-model.md`](knowledge/cost-model.md) | ON-DEMAND | Where the token and credit spend goes per phase, and the levers ranked by saving |

### Artefact templates

| File | Used at | Output |
|------|---------|--------|
| [`templates/README.md`](templates/README.md) | always | The step → template map and canonical output filenames |
| [`templates/raw-requirement-new-feature.md`](templates/raw-requirement-new-feature.md) | `A0` | `work/<slug>/<Feature>.md` |
| [`templates/raw-requirement-enhancement.md`](templates/raw-requirement-enhancement.md) | `B0` | `work/<slug>/<Enhancement>.md` |
| [`templates/raw-requirement-bug-fix.md`](templates/raw-requirement-bug-fix.md) | `C0` | `work/hotfixes/<slug>/README.md` |
| [`templates/relevant-info.md`](templates/relevant-info.md) | `A1` `B1` | `work/<slug>/relevant_info.md` |
| [`templates/do-not-break.md`](templates/do-not-break.md) | `B1` | `work/<slug>/do-not-break.md` |
| [`templates/feature-steering.md`](templates/feature-steering.md) | `A2` `B2` | `rules/project/8x-<slug>.md` |
| [`templates/all-gaps.md`](templates/all-gaps.md) | `A3` `B3` | `work/<slug>/all_gaps.md` |
| [`templates/final-requirement.md`](templates/final-requirement.md) | `A4` `B4` | `work/<slug>/final_requirement.md` |
| [`templates/design-00-architecture-overview.md`](templates/design-00-architecture-overview.md) | `A5` `B5` | `work/<slug>/design/00-architecture-overview.md` |
| [`templates/design-section.md`](templates/design-section.md) | `A5` `B5` | `work/<slug>/design/design-0N-<topic>.md` |
| [`templates/review-round.md`](templates/review-round.md) | `A6` `A8` `B6` `B8` | `design/design-review-round-N.md`, `tasks/tasks-review-round-N.md` |
| [`templates/tasks-readme.md`](templates/tasks-readme.md) | `A7` `B7` | `work/<slug>/tasks/README.md` |
| [`templates/task-details.md`](templates/task-details.md) | `A7` `B7` | `work/<slug>/tasks/task_details.md` |
| [`templates/final-review-report.md`](templates/final-review-report.md) | `A8` `B8` | `work/<slug>/final-review-report.md` |
| [`templates/execution-report.md`](templates/execution-report.md) | `A9`–`A10` `B9` | `work/<slug>/tasks/execution-report-S<n>.md` |
| [`templates/cutover-report.md`](templates/cutover-report.md) | `A11` `B10` | `work/<slug>/tasks/cutover-report.md` |
| [`templates/manual-testing.md`](templates/manual-testing.md) | `A12` `B11` | `work/<slug>/tasks/manual-testing.md` |
| [`templates/fix-report.md`](templates/fix-report.md) | `C2`–`C6` | `work/hotfixes/<slug>/fix-report.md` |

### Task-type formats — the shape of a task plan per discipline

| File | Applies to |
|------|-----------|
| [`templates/task-formats/README.md`](templates/task-formats/README.md) | Which format to pick, and the blocks common to all of them |
| [`templates/task-formats/ui-task.md`](templates/task-formats/ui-task.md) | Screens, components, styling, accessibility, responsive behaviour |
| [`templates/task-formats/backend-api-task.md`](templates/task-formats/backend-api-task.md) | Endpoints, handlers, service logic, authz, contracts |
| [`templates/task-formats/database-task.md`](templates/task-formats/database-task.md) | Schema, migrations, indexes, backfills, data integrity |
| [`templates/task-formats/infra-devops-task.md`](templates/task-formats/infra-devops-task.md) | CI, pipelines, infrastructure-as-code, environments, releases |
| [`templates/task-formats/data-ml-task.md`](templates/task-formats/data-ml-task.md) | Pipelines, feature engineering, model training, evaluation, drift |
| [`templates/task-formats/integration-task.md`](templates/task-formats/integration-task.md) | Third-party APIs, webhooks, message queues, idempotency |

### Retrieval

| File | Purpose |
|------|---------|
| [`retrieval/index-spec.md`](retrieval/index-spec.md) | The three retrieval tiers, the tier-decision rule, and the full spec for a local zero-cost retrieval index when a project is large enough to need one |
| [`retrieval/build-index.md`](retrieval/build-index.md) | **How to actually build it.** The `S7` procedure, with a deliverable per tier — tier 0 included. Run it when install-audit row A5.2 fails |

### Adapters

| File | Purpose |
|------|---------|
| [`adapters/README.md`](adapters/README.md) | How to write an adapter, and the reference-not-copy rule |
| [`adapters/ide-matrix.md`](adapters/ide-matrix.md) | Known instruction mechanisms per IDE and which load modes each supports |
| [`adapters/AGENTS.template.md`](adapters/AGENTS.template.md) | The universal fallback adapter most assistants read natively |
| [`adapters/adapters-log.md`](adapters/adapters-log.md) | One entry per IDE set up, what degraded, and the drift hashes for any inlined content |

### Setup

| File | Purpose |
|------|---------|
| [`setup/activation-protocol.md`](setup/activation-protocol.md) | The `super_saiyan_bann_jo` runbook, steps `S0`–`S9` |
| [`setup/discovery-checklist.md`](setup/discovery-checklist.md) | The evidence-gathering commands for `S1` and `S2`, and what each finding decides |
| [`setup/stack-profiles.md`](setup/stack-profiles.md) | Gate-role → concrete-command mapping for 11 ecosystems, plus how to add one |
| [`setup/install-audit.md`](setup/install-audit.md) | The `S9` artefact audit — 69 rows across 7 blocks. Does the deliverable exist and is it filled? Run any time an install seems not to be working |

---

## Zone 2 — project

> Generated at `S7`. In this pristine copy it is empty by design. In an installation, every row below is
> real and current, and the auto-healing rule keeps it that way.

### Project documentation

| Doc | Covers | Status | Last verified |
|-----|--------|--------|---------------|
| _(generated at `S7` — one row per project document, with a status of current / stale / missing)_ | | | |

### Active work

| Slug | Track | Stage | Artefacts | Status |
|------|-------|-------|-----------|--------|
| _(one row per in-flight feature, enhancement or hotfix; this is the resume point after a break)_ | | | | |

### Shipped work

| Slug | Track | Shipped | Lasting record |
|------|-------|---------|----------------|
| _(moved here at close-out, when the per-feature steering file is deleted)_ | | | |

### Known documentation debt

| Gap | Severity | Raised by | Plan |
|-----|----------|-----------|------|
| _(anything the auto-healing rule deferred rather than fixed on the spot, with the reason)_ | | | |
