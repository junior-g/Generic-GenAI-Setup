<!--
TEMPLATE — Track A intake. Copy to <artefact_root>/<slug>/<Feature-Name>.md, delete this header, fill it in.

WHO FILLS THIS: the user, before the session starts. The agent may help, but the decisions are the user's.

WHY IT MATTERS: this is the cheapest point in the entire process to spend five minutes. Every question
answered here is a gap round avoided, and a gap round costs a full read-write cycle over the requirement
plus a wait. The out-of-scope line and the definition-of-done section are the two highest-yield blocks in
the framework.

Sections are not deleted. Write None or Not applicable.
-->

# <Feature Name>

## Frame

Fill this first. It selects the track, the reversibility posture, and the budget before any file is read.

| | |
|---|---|
| **Track** | A — new feature |
| **Slug** | `<lowercase-hyphenated>` — used in the folder, the steering filename, and the index row |
| **One-line goal** | |
| **New actors** | <or none> |
| **Surfaces created** | |
| **Surfaces touched** | <existing ones this brushes against> |
| **Irreversible surfaces involved** | <live data, permissions, mail, payments — or none> |
| **Phasing** | <one pass, or the phases and their order> |
| **Session budget** | <e.g. "gap analysis and requirement only" / "S0 and S1" / "two hours"> |
| **Priority** | 🔴 blocking / 🟡 planned / 🟢 opportunistic |

## AIM

<Two to four sentences. What capability this adds and why it is worth building. The agent restates this at
every session start, so write it as something worth re-reading.>

## Definitions

Terms this brief uses that the codebase may not. One row each — a mismatch here becomes three artefacts
using three names for the same thing.

| Term | Means here | Existing code name, if any |
|------|-----------|---------------------------|
| | | |

## Actors and capabilities

| Actor | New capability | Existing? | Entry point |
|-------|----------------|-----------|-------------|
| | | yes / no | <how they reach it> |

An actor with a capability and no entry point is stranded. Naming the entry point here is what prevents it.

## Desired behaviour

Numbered, atomic statements. These become `R`-numbers directly, so one behaviour per line and no compound
statements.

1. <behaviour>
2. <behaviour>
3. <behaviour>

## Surfaces

| Surface | Kind | Actor | Purpose |
|---------|------|-------|---------|
| | screen / endpoint / job / command / consumer | | |

## Data

| Entity | New? | Holds | Live data exists? |
|--------|------|-------|-------------------|
| | | | n/a for new |

<Anything you already know about volume, growth, or retention. "Unknown" is a fine answer and better than a
guess.>

## Out of scope

**The highest-yield section in this document.** Everything a reasonable reader might assume is included and
is not.

- <not this>
- <not this>
- <not this>

## Must not change / must not break

Existing behaviour this feature must leave alone. Seeds the regression checks even on Track A.

| Must keep working | Why it matters | How you would notice it broke |
|-------------------|----------------|-------------------------------|
| | | |

## Definition of done

**Written by you, not by the agent.** This is what removes the agent's freedom to redefine "done" as "the
part I finished". Be concrete enough that each line is observably true or false.

- [ ] <observable outcome>
- [ ] <observable outcome>
- [ ] All four gates green
- [ ] <locale parity, if applicable>

## Open questions I already know

Every row here collapses a gap round. Your leaning matters even when you are unsure — it gives the agent a
reversible default instead of a blocking question.

| # | Question | My leaning | Firm? |
|---|----------|-----------|-------|
| 1 | | | yes / no / need advice |

## Reference material

Anything the agent would otherwise have to search for.

| What | Where |
|------|-------|
| Similar existing feature | `path` |
| Designs / screenshots / mockups | `path` |
| External API documentation | <url> |
| Prior discussion or decision | `path` |

## Non-functional expectations

Only what is genuinely a constraint. An invented budget is worse than none.

| Concern | Expectation |
|---------|-------------|
| Performance | |
| Scale | |
| Availability | |
| Security / privacy | |
| Accessibility | |
| Observability | |

## Steps to be followed

<Default is the full Track A sequence `A0`–`A12`. Override here if this session should do something
different — "stop after the requirement", "skip the manual testing doc, I'll test by hand". This line lets
you change the process per session without renegotiating it.>

## Notes

<Anything else. Constraints, deadlines, people to consult, things that went wrong last time.>
