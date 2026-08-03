<!--
TEMPLATE — per-feature context anchor. Copy to <rules_root>/8x-<slug>.md, delete this header, fill it in.
Written at step A2 / B2. Filled by the AGENT.

THE CHEAPEST ARTEFACT IN THE FRAMEWORK, and it addresses three failure modes at once: F2 goal drift,
F4 context overflow, F10 positional attention bias.

HOW IT IS USED: pulled in at EVERY session start and EVERY section boundary — not just at the beginning.
Re-anchoring works because it puts the objective at the END of the working context, where attention is
strongest.

KEEP IT SHORT. Under 60 lines. A long steering file defeats its own purpose: it stops being re-read.

DELETE IT AT CLOSE-OUT. The feature has shipped; the work folder is the lasting record. Leaving feature
files to accumulate in the rule set recreates the instruction-surface problem the framework exists to avoid.
-->

# <Feature / Enhancement Name>

| | |
|---|---|
| Track | A / B / B-lite |
| Slug | `<slug>` |
| Work folder | `<artefact_root>/<slug>/` |
| Current stage | <updated as it moves> |
| Caps | gap `<n>` / design `<n>` / tasks `<n>` |
| Retire this file | at close-out |

## AIM

<Two sentences maximum. What this work achieves. Re-read at every load — write it to be re-read.>

## In scope

- <thing>
- <thing>

## Out of scope

**Binding.** Anything here that appears in the work is scope creep, and gets removed or raised as an
amendment.

- <thing>
- <thing>

## Key context files

The pinned working set. From `relevant_info.md` §12. **Prune anything never actually read.**

| File | Why |
|------|-----|
| `path` | |

<More than a handful means this is a directory sweep with extra steps. If the list keeps growing, the work
needs phasing.>

## Must not break

Summary only — the full register is `<artefact_root>/<slug>/do-not-break.md`.

| Contract | Notice-it-broke signal |
|----------|----------------------|
| | |

## Entry points

Only if permissions or actor access are touched. An actor with a capability and no entry point is stranded.

| Actor | Reaches | Via |
|-------|---------|-----|
| | | |

## Decisions already settled

So they are not re-litigated in a later session. One line each.

| # | Decision | Settled at |
|---|----------|-----------|
| 1 | | `A3` round 2 / user answer / design review DR-4 |

## Current position

The resume pointer. Updated at every section boundary — this is what a "continue" reads first.

| | |
|---|---|
| Last completed | <step or section> |
| Next | <step or section> |
| Open follow-ups | <count, and where they are listed> |
| Latest execution report | `path` |

## Session budget

| | |
|---|---|
| Declared | <from the raw requirement> |
| Consumed so far | <stages or sections completed> |
| Check at | every stage boundary |
