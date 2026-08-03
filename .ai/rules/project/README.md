# Project Rule Layer

The rules that are **different in every project**. Generated once at activation step `S5`, then maintained
by the project.

The split matters. [`../`](../00-agent-contract.md) `00`–`95` are **universal**: copied verbatim, never
edited in place, safe to re-sync when the framework is upgraded. This folder is **yours**: the framework
never overwrites it.

---

## What gets generated

| Template | Output | Load mode | Generate when |
|----------|--------|-----------|---------------|
| `product.template.md` | `product.md` | ALWAYS | always |
| `structure.template.md` | `structure.md` | ALWAYS | always |
| `tech.template.md` | `tech.md` | ALWAYS | always |
| `10-code-conventions.template.md` | `10-code-conventions.md` | ALWAYS | always |
| `15-design-system.template.md` | `15-design-system.md` | FILE-MATCH on UI globs | the project has a user interface |
| `20-api-standards.template.md` | `20-api-standards.md` | FILE-MATCH on API globs | the project exposes endpoints, commands, or consumers |
| `30-data-and-types.template.md` | `30-data-and-types.md` | FILE-MATCH on data globs | the project persists data |
| `90-docs-map.template.md` | `90-docs-map.md` | ON-DEMAND | the project has documentation of its own |

Plus, transiently: `8x-<slug>.md` per in-flight feature, from
[`../../templates/feature-steering.md`](../../templates/feature-steering.md). Created at `A2`/`B2`,
**deleted at close-out**.

**Generate only what exists.** A command-line tool gets no design system. A stateless service gets no data
rules. When you skip one, record the decision in the "Omitted" table at the bottom of this file so a later
session knows it was a judgement, not an oversight.

## The rule for generating these

Every statement in a generated rule carries a `path:line` pointer to the code that establishes it.

| Situation | What to write |
|-----------|---------------|
| The pattern is consistent across three or more call sites | State it as the convention, cite two of them |
| You saw it once | **Not a convention.** Check more sites, or leave it out |
| The codebase is genuinely inconsistent | Say so, name both patterns, state which is preferred going forward, and cite an example of each |
| The right answer is a matter of taste and nobody has decided | Leave it out. An invented rule is worse than no rule, because it gets enforced |
| A pattern is clearly wrong but pervasive | Record it in [`../70-do-not-regress.md`](../70-do-not-regress.md) Part 2 with the correct pattern, and note that existing code has not been migrated |

A rule you inferred rather than verified is failure mode **F5** with a long tail: it will be enforced by
every future session, on every file, until someone notices.

## Keeping them current

These files describe a moving target. Three mechanisms keep them honest:

1. **Auto-healing** — [`../65-auto-healing-docs.md`](../65-auto-healing-docs.md). A rule contradicted by the
   code is a contradicted document. Code wins; fix the rule.
2. **Close-out** — Track A `A12` / Track B `B11` sync docs for whatever the change made stale. A feature that
   introduced a new layer usually means a new section here.
3. **Conflict reporting** — two rules instructing differently is **F11**. Stop and ask which wins rather
   than picking one silently.

## Size discipline

Each generated file should be readable in under two minutes. These are ALWAYS-loaded files: every line costs
context on every request, forever.

| Symptom | Fix |
|---------|-----|
| Longer than roughly 150 lines | Split the detail into a project document and leave a pointer |
| Explaining *why* at length | The why belongs in a design doc or an ADR; the rule states what to do |
| Repeating something from the universal rules | Delete it. Duplication is **F11** waiting to happen |
| Listing every constant | Point at the constants module instead |
| Describing a feature | Features live in `<artefact_root>/<slug>/**`, not in a rule |

## Generation log

Filled at `S5`. It is the record of what was decided and on what evidence.

| File | Generated | Evidence base | Notes |
|------|-----------|---------------|-------|
| | | | |

## Omitted deliberately

| Template | Why not generated |
|----------|-------------------|
| | |
