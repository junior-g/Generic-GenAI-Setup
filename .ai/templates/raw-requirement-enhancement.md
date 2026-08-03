<!--
TEMPLATE — Track B intake. Copy to <artefact_root>/<slug>/<Enhancement>.md, delete this header, fill it in.

WHO FILLS THIS: the user, before the session starts.

HOW THIS DIFFERS FROM TRACK A: an enhancement's risk is not "what to build", it is "what breaks". So this
template front-loads current state, live data, and the contracts that must survive.

IMPORTANT: your description of current behaviour is A CLAIM TO BE VERIFIED, not an instruction. The agent
will check it. Being wrong here is USEFUL — it gets caught at B1 instead of at runtime. Do not soften a
description to make it sound more certain than it is.

Sections are not deleted. Write None or Not applicable.
-->

# <Enhancement Name>

## Frame

| | |
|---|---|
| **Track** | B — enhancement <or "B-lite", if one surface, no schema change, no permission change> |
| **Slug** | `<lowercase-hyphenated>` |
| **One-line goal** | |
| **Feature being changed** | <the existing feature, and where its docs are> |
| **Surfaces touched** | |
| **Live data exists on these surfaces?** | **yes / no** — if yes, migration and back-compat are in scope by default |
| **New actors or capabilities** | <or none> |
| **Irreversible surfaces involved** | <or none> |
| **Session budget** | |
| **Priority** | 🔴 / 🟡 / 🟢 |

## AIM

<Two to four sentences. What changes and why. Restated by the agent at every session start.>

## Current state — as I understand it

**A claim to be verified.** Describe what happens today, and mark your confidence honestly.

| Aspect | What happens today | Confident? |
|--------|-------------------|-----------|
| | | yes / think so / guessing |

<Where you are guessing, say so. A "guessing" row costs the agent one verification. A wrong "confident" row
costs a stage.>

## Gap — current vs wanted

The core of this document. One row per delta.

| # | Today | Wanted | Why it matters |
|---|-------|--------|----------------|
| 1 | | | |
| 2 | | | |

## Desired behaviour

Numbered, atomic. These become `R`-numbers.

1. <behaviour>
2. <behaviour>

## Impact per surface — as I expect it

Your expectation, to be verified and corrected at `B1`/`B6`. Wrong entries here are useful findings.

| Surface | Expected change | Risk if it goes wrong |
|---------|-----------------|----------------------|
| | | |

## Must not break

**The most important section in this document.** It seeds `do-not-break.md`, which is the only real control
against the unknown-consumer failure. A blank section here has historically produced wrong "impact-free"
claims and extra review rounds.

| Contract | Consumers I know of | How you would notice it broke |
|----------|--------------------|-------------------------------|
| | | |

Cover, where relevant: routes and screens · API response fields · event payloads · schema fields ·
permissions and entry points · localised keys · shared components and styles · existing tests · public
exports.

### Contracts I am deliberately breaking

| Contract | Why | Migration path I have in mind |
|----------|-----|------------------------------|
| | | |

<Empty is normal. A deliberately broken contract is a decision; a silently broken one is an outage.>

## Data availability

Only if the change touches persisted data.

| Question | Answer |
|----------|--------|
| Does the data needed already exist? | |
| Approximate record count on the affected entity | |
| Records that predate the new field — what should they read as? | |
| Is a backfill acceptable? | yes / no / needs discussion |
| Is downtime acceptable? | |
| Reversal expectation if it goes wrong | |

## Out of scope

- <not this>
- <not this>

## Definition of done

**Written by you.**

- [ ] <observable outcome>
- [ ] Every row of `do-not-break.md` verified green
- [ ] All four gates green
- [ ] <migration verified, or its limits stated>

## Open questions I already know

| # | Question | My leaning | Firm? |
|---|----------|-----------|-------|
| 1 | | | |

## Reference material

| What | Where |
|------|-------|
| The feature's existing docs | `path` |
| The code I believe is involved | `path` |
| Prior related work | `path` |
| Designs, if the UI changes | `path` |

## Non-functional expectations

| Concern | Expectation | Changed by this work? |
|---------|-------------|----------------------|
| Performance | | |
| Security / privacy | | |
| Accessibility | | |

## Steps to be followed

<Default is the full Track B sequence `B0`–`B11`. State "B-lite" here if you want the reduced set, and say
why it qualifies — one surface, no schema change, no permission change.>

## Notes

<Anything else. Including: has this been attempted before, and what happened?>
