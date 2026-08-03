<!--
TEMPLATE — Track C intake. Copy to <artefact_root>/hotfixes/<slug>/README.md, delete this header, fill it in.

WHO FILLS THIS: the user, when reporting the defect.

THE HIGHEST-VALUE BLOCK IS THE WORKS/BROKEN MATRIX. "Broken on mobile, fine on desktop" or "fails for the
second request in a session, not the first" often localises the cause to one file before any code is read.
Spend your effort there rather than on prose.

WITHOUT REPRODUCTION STEPS THE AGENT WILL STOP AND ASK. That is correct behaviour — an unreproduced fix
cannot be verified, and edits to working code are a net loss.

Sections are not deleted. Write None or Not applicable.
-->

# Bug — <short symptom>

## Frame

| | |
|---|---|
| **Track** | C — bug fix |
| **Slug** | `<lowercase-hyphenated>` |
| **Severity** | 🔴 blocks a journey / 🟡 degrades a journey / 🟢 cosmetic |
| **First noticed** | <date, and whether it ever worked> |
| **Environment** | <local / staging / production; version or build if known> |
| **Frequency** | always / intermittent / once |
| **Affected actors** | |
| **Workaround available?** | |

## Symptom

<What you see, in one paragraph. Observable behaviour only — no theory about the cause. A stated cause in
this section anchors the investigation and makes a wrong theory harder to abandon.>

## Expected vs actual

| | |
|---|---|
| **Expected** | |
| **Actual** | |
| **Where expected comes from** | <a requirement, a document, the obvious reading, prior behaviour> |

## Reproduction steps

Numbered, from a known starting state. Include the data and the actor.

1. <start from…>
2. <do…>
3. <observe…>

| | |
|---|---|
| Starting state / data needed | |
| Actor / permission required | |
| Reproduces every time? | |
| Reproduces in which environments | |

## Works / broken matrix

**Fill this properly.** The contrast is the localisation signal, and a root cause that does not explain the
contrast is not the cause.

| Condition | Works | Broken |
|-----------|-------|--------|
| Actor / role | | |
| Environment | | |
| Device / client / viewport | | |
| Data state | | |
| Sequence or timing | | |
| Locale / region | | |
| First attempt vs subsequent | | |
| Other axis you noticed | | |

<Any row you can fill on both sides narrows the search. A row where you only know one side is still worth
recording.>

## Evidence

| Kind | Where |
|------|-------|
| Error message, verbatim | |
| Log excerpt | `path` or paste |
| Screenshot / recording | `path` |
| Failing request/response | |
| Stack trace | |
| Related incident or ticket | |

<Paste the exact error text rather than describing it. The exact string is searchable; a description is not.>

## Recent changes

| Question | Answer |
|----------|--------|
| Did it work before? When? | |
| What changed around then? | <deploy, dependency upgrade, config change, data migration> |
| Suspect commit or release, if any | |

## The fence — what the fix may and may not touch

The scope boundary. It is what stops a hotfix becoming a refactor.

| | |
|---|---|
| **May touch** | |
| **Must not touch** | |
| **Must not change behaviour of** | |

## What I have already tried

| Attempt | Result | What it rules out |
|---------|--------|-------------------|
| | | |

<Empty is fine. Filled is better — it stops the agent re-walking a dead end.>

## My theory, if I have one

<Optional, and explicitly labelled as a theory. The agent will verify or reject it, and a rejected theory is
recorded rather than forgotten. State it separately from the symptom so it does not contaminate the
description.>

## Definition of done

- [ ] The reproduction above no longer produces the reported behaviour
- [ ] The working condition from the matrix still works
- [ ] A regression test fails-before / passes-after — or a justified manual check
- [ ] All four gates green
- [ ] Root cause written before the edit

## Notes

<Deadline, blast radius, who else is affected, whether a rollback is available.>
