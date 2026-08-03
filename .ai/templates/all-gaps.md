<!--
TEMPLATE — gap analysis. Copy to <artefact_root>/<slug>/all_gaps.md, delete this header, fill it in.
Written at step A3 (max 3 rounds) / B3 (max 2 rounds, B-lite 1). Filled by the AGENT, answered by the USER.

CAPS ARE HARD. NO ESCAPE HATCH. At the cap the stage closes: open 🟡/🟢 items become residual risks in the
final review report, and an open 🔴 blocks the stage and is REPORTED rather than absorbed. Wanting another
round is F7.

STOP AFTER EACH ROUND and wait for answers. STOP means wait for the user; it is never a self-resolving loop.

ROUND N+1 HUNTS ONLY GAPS OPENED BY THE USER'S ANSWERS. It never re-treads settled ground — re-asking a
settled question is how three rounds become six.

Sections are not deleted. Write None or Not applicable.
-->

# Gap Analysis — <slug>

| | |
|---|---|
| Track | A / B / B-lite |
| Cap | <3 / 2 / 1> rounds — **hard, no escape hatch** |
| This round | <n> of <cap> |
| Grounded on | `relevant_info.md` <and `do-not-break.md` on Track B> |
| Status | awaiting answers / answered / closed at cap |

## AIM restated

<One or two sentences, so this file reads standalone.>

## How to answer

Fill each `Your answer:` line. `Agree` is a complete answer when the recommendation is right. Where you do
not know, say so and pick the reversible default — an honest "not sure, go with your recommendation" is
better than a guess that gets built.

Severity meanings:

| | Meaning | If unanswered at the cap |
|---|---------|--------------------------|
| 🔴 | The work cannot proceed correctly without an answer | **Blocks the stage.** Reported, not absorbed |
| 🟡 | Proceeding is possible but a wrong assumption is likely | Becomes a residual risk with the default applied |
| 🟢 | A refinement | Becomes a residual risk; default applied silently |

---

## Round <n>

Grouped by theme, not by file — themes are how a user answers efficiently.

### Theme: <theme name>

#### G<n>.1 — <short title> 🔴

**Observation.** <What the survey found, with `path:line`. Fact, not opinion.>

**Why it is a gap.** <What is undetermined, and what breaks if it is guessed wrong.>

**Recommendation.** <The best-practice answer and *why*, in one or two sentences. A question with no
recommendation makes the user do work the agent should have done.>

**Question.** <One question. Not three bundled into one sentence.>

**Options.**

| Option | Consequence | Reversible? |
|--------|-------------|-------------|
| A — <recommended> | | |
| B | | |

**Your answer:**

---

#### G<n>.2 — <short title> 🟡

**Observation.**

**Why it is a gap.**

**Recommendation.**

**Question.**

**Your answer:**

---

### Theme: <theme name>

#### G<n>.3 — <short title> 🟢

**Observation.**

**Why it is a gap.**

**Recommendation.**

**Question.**

**Your answer:**

---

## Round <n> summary

| Gap | Severity | Answered? | Resolution |
|-----|----------|-----------|------------|
| G<n>.1 | 🔴 | | |

**STOP — waiting for answers.**

---

## Answers received — round <n>

Filled after the user responds. Each answer either settles a gap or opens a new one; only the newly opened
ones are pursued next round.

| Gap | Answer | Settles it? | Opens new gaps? |
|-----|--------|-------------|-----------------|
| G<n>.1 | | yes / partly | <new gap ids, or none> |

### Consequences of these answers

| Answer | Changes | Where recorded |
|--------|---------|----------------|
| | <requirement / design / task implication> | |

---

## Round <n+1>

<Only gaps opened by the answers above. If the answers opened none, say so and close the stage — an
unnecessary round is F7.>

---

## Closure

| | |
|---|---|
| Rounds used | <n> of <cap> |
| Gaps raised | <n> |
| Gaps settled | <n> |
| Open at closure | <n> |
| Closed because | all settled / **cap reached** |

### Open at closure — carried forward

Every one of these appears in `final_requirement.md`, inline as a multiple choice with the reversible default
selected, and again in `final-review-report.md` as a residual risk.

| Gap | Severity | Default applied | Reversible? | Risk if the default is wrong |
|-----|----------|-----------------|-------------|------------------------------|
| | | | | |

### 🔴 open at the cap

| Gap | Why it blocks | Reported to user |
|-----|---------------|------------------|
| | | ☐ |

**A 🔴 here blocks the stage.** It is reported and the stage stops. It does not buy another round, and it is
not silently downgraded to 🟡 to let the work continue.
