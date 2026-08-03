# AI Troubleshooting — limitations, and the guardrails that hold

**Load mode: ON-DEMAND.** Read this when something is going wrong and you want to know *why* it goes wrong,
not just what to do about it.

The rest of the framework tells you what to do. This file explains the machine you are operating, so the
rules stop looking arbitrary. Every guardrail here exists because a specific limitation is structural — not
a bug that a better model release fixes, and not something a more carefully worded prompt avoids.

Companion files: [`failure-taxonomy.md`](failure-taxonomy.md) for the `F1`–`F13` reference,
[`../rules/05-failure-detection.md`](../rules/05-failure-detection.md) for the runtime table,
[`../rules/84-failure-playbooks.md`](../rules/84-failure-playbooks.md) for remediation.

---

## The five structural limitations

Everything downstream follows from these. They are properties of how the system works, not defects.

| # | Limitation | Consequence |
|---|-----------|-------------|
| 1 | **The model predicts plausible text.** It has no separate faculty that knows whether a statement is true | A fluent wrong answer costs the same to produce as a right one, and reads identically |
| 2 | **Context is finite and attention within it is uneven.** Material in the middle of a long context influences output less than material at either end | A constraint given early stops working, silently, with no error |
| 3 | **There is no persistent memory between sessions**, and within a session memory is just the context | Anything not written down is gone. Confidence about "what we decided" is reconstruction |
| 4 | **There is no execution model of the code being written** unless something actually runs it | "This should work" is a prediction. Only a gate is evidence |
| 5 | **The training objective rewards helpfulness and agreement**, not correctness under disagreement | Agreeing is the path of least resistance, and it feels cooperative |

---

## 1. Hallucination and confabulation

### What it actually is

Not lying, and not a knowledge gap. The model produces the most plausible continuation. When the plausible
continuation is a function name, a config key, a CLI flag, or a file path that *should* exist given
everything else, it appears — with the same confidence as a fact that was read from the file.

**The tell is absent by design.** There is no internal signal distinguishing "I read this" from "this is what
a codebase like this would contain". That is why the remedy cannot be introspective.

### Where it shows up in coding work

| Form | Example |
|------|---------|
| Invented API surface | a method on a library that has the right name for the job and does not exist |
| Invented configuration | a flag or key that fits the pattern of the real ones |
| Invented file paths | a path consistent with the project's conventions, but not present |
| Confident negatives | "there is no existing rate limiter" after one search that was pointed at the wrong directory |
| Plausible version behaviour | a pattern that was correct two major versions ago |
| Fabricated evidence | a line number, a test name, or a coverage figure that was never observed |

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **A pointer is not evidence** | [`00-agent-contract.md`](../rules/00-agent-contract.md) §1 | Moves the standard from "I believe" to "I opened it". Belief is not inspectable; a `path:line` is |
| **Negatives need two independent checks by different methods** | same | A single scoped search failing is the *expected* result of a badly aimed search. Two methods that disagree expose it |
| **Quote, don't paraphrase, for 🔴 claims** | [`62-3x-verification.md`](../rules/62-3x-verification.md) P1.1 | Reading the right file and misreading it are different failures. A quote catches the second |
| **Version-specific rules in `tech.md`** | [`project/tech.template.md`](../rules/project/tech.template.md) | Deprecated-but-plausible patterns are the hardest hallucination to spot, because they were once true |
| **No invented figures** | [`00-agent-contract.md`](../rules/00-agent-contract.md) §4 | Numbers are the most convincing fabrication and the easiest to check |
| **The gates** | [`60-workflow-and-verification.md`](../rules/60-workflow-and-verification.md) | An invented API fails the static gate. Running it is cheaper than reviewing for it |

### The one that matters most

The **negative claim**. A wrong positive claim usually fails a gate within minutes. A wrong negative —
"this doesn't exist", "nothing depends on this", "removing it is safe" — produces confident, coherent work
built on absence, and absence is not something a gate can check.

Both of the most expensive wrong claims in this framework's history were negatives. Both came from a single
scoped search. Both survived two review stages.

---

## 2. Context loss

### What it actually is

Three distinct problems that present the same way.

| Problem | Mechanism | Signature |
|---------|-----------|-----------|
| **Overflow** | accumulated material exceeds the window; the oldest content is dropped | early instructions stop applying; output truncates mid-structure |
| **Positional bias** | attention degrades toward the middle of a long context | a rule from the middle of a long document is ignored while rules at either end are followed |
| **Session boundary** | a new session, or a compaction, starts from a summary rather than the original | confident statements about decisions that were never made |

The dangerous property they share: **no error is raised.** The work continues, fluently, without the
constraint. There is nothing to catch.

### Where it shows up

- A convention followed for the first hour and violated for the second.
- The same file opened for the third time because its content fell out of context.
- An out-of-scope item built anyway, forty minutes after the out-of-scope list was read.
- A "continue" that redoes finished work, or contradicts it.
- A long document's middle section quietly ignored.

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **Externalised state** — status tables, execution reports, follow-up lists | [`../templates/tasks-readme.md`](../templates/tasks-readme.md), [`execution-report.md`](../templates/execution-report.md) | Completion state outside the context window cannot fall out of it |
| **Resume from artefacts, never memory** | [`60-workflow-and-verification.md`](../rules/60-workflow-and-verification.md) | Removes the option of reconstructing, which is where invention enters |
| **Per-feature steering, re-pinned at every session and section boundary** | [`../templates/feature-steering.md`](../templates/feature-steering.md) | A short file re-read often beats a long one read once. It re-anchors the objective at the *end* of context, where attention is strongest |
| **Short ALWAYS files with a line budget** | [`project/README.md`](../rules/project/README.md) | Every line of an always-on file costs context on every request, forever |
| **Pinned working set, pruned** | [`95-retrieval.md`](../rules/95-retrieval.md) | Names the few files that matter instead of re-reading many |
| **No wide directory reads** | same | The single largest source of avoidable context consumption |
| **Non-negotiables inlined in `MAIN.md`** | [`../../MAIN.md`](../../MAIN.md) | Deliberate exception to reference-don't-copy: they must survive positional decay |
| **Restate a slipping constraint at the end of context** | **F10** first move | Works *with* positional bias instead of against it |

### Why "just use a bigger window" is not the answer

A larger window relocates the problem. Positional bias scales with length — a rule at token 200,000 of
400,000 is less influential than the same rule at token 20,000 of 40,000. More room also removes the pressure
that keeps grounding narrow, and unfocused grounding is the thing that fills the window in the first place.

---

## 3. Code-generation loops

### What it actually is

The model has no execution model of the code. When an attempt fails, the natural next step is *a variation of
the same idea*, because the underlying theory of the failure has not changed. Each attempt is locally
reasonable. The sequence is a loop.

Two distinct shapes:

| Shape | Mechanism | Signature |
|-------|-----------|-----------|
| **Thrashing** (**F3**) | same theory, different parameters | same call, same error, three times, with an apology between each |
| **Paralysis** (**F7**) | planning avoids being wrong, so planning expands | many different read-only calls, hedging language, no file written |

They look similar from outside — no progress — and need opposite responses. Thrashing needs a *different
theory*. Paralysis needs a *commitment to act*.

### Why more attempts do not help

Attempt three of the same theory carries the same information as attempt one. The useful output of two
failures is not a third attempt: it is **the set of explanations those two failures rule out**. That set is
what changes the theory, and it only exists if you write it down.

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **Two-strike rule** | [`00-agent-contract.md`](../rules/00-agent-contract.md) §8 | A hard count, checkable without judgement. "Am I making progress?" is exactly the question a loop answers wrongly |
| **Write down what the failures rule out** | **F3** correction | Converts a loop into information. Also makes the loop visible to the user |
| **Root cause in writing before the first edit** | [`83-track-c-bug-fix.md`](../rules/83-track-c-bug-fix.md) `C2` | Gives a theory to falsify instead of a guess to tweak. A report that exists before the fix cannot be written retroactively |
| **A cause must explain the works/broken contrast** | same | Kills plausible-but-wrong causes before any code is touched |
| **Hard round caps, no escape hatch** | every track file | Paralysis cannot expand to fill the budget when the budget is a hard count |
| **A review round emits findings or names files read** | [`../templates/review-round.md`](../templates/review-round.md) | A round cannot be spent producing nothing |
| **Fails-before / passes-after, both observed** | `C5` | Proves the fix addressed the defect, not that the code does what it does |
| **Discard and regenerate, not patch** | [`62-3x-verification.md`](../rules/62-3x-verification.md) | Patching a structurally wrong artefact is the loop in document form |
| **Second discard on the same artefact is F3** | same | Catches the loop even when it is happening in prose rather than code |

---

## 4. Sycophancy and premature completion

### What it actually is

Two behaviours from one cause: agreement and closure are both rewarded, and both are cheaper than
verification.

- **Sycophancy** — accepting the user's framing because it is the user's. A stated assumption goes unchecked. A
  review finds nothing and names nothing. A correction gets softened until it no longer lands.
- **Premature completion** — declaring done because the visible work is done. The gate was not run, the
  follow-up is open, the acceptance criterion is *plausibly* met rather than observably met.

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **The user's stated current behaviour is a claim to be verified** | [`../templates/raw-requirement-enhancement.md`](../templates/raw-requirement-enhancement.md) | Removes the social cost of contradicting the brief by framing a wrong premise as a useful finding |
| **A review outcome is findings, or a sign-off naming every file read** | [`review-round.md`](../templates/review-round.md) | "Looks good" becomes structurally unavailable |
| **Definition of done written by the user, in advance** | raw-requirement templates | Removes the freedom to redefine done as "what I just finished" |
| **Follow-ups block section closure** | [`execution-report.md`](../templates/execution-report.md) | Mechanical, not a judgement call |
| **Real gate output required, not a claim** | [`60-workflow-and-verification.md`](../rules/60-workflow-and-verification.md) | "Gates pass" is unfalsifiable; pasted output is not |
| **Correct the user when they are wrong** | [`00-agent-contract.md`](../rules/00-agent-contract.md) §4 | Makes disagreement the expected behaviour rather than a risk |
| **Three verification passes with named discard triggers** | [`62-3x-verification.md`](../rules/62-3x-verification.md) | Confidence cannot substitute for a pass that produces a recorded outcome |

---

## 5. Goal drift

### What it actually is

The original objective is thousands of tokens back. The current sub-task is right here, specific and vivid.
Attention follows salience, and "done" quietly becomes "the thing I am doing".

It is not confusion. Each individual step is defensible. The trajectory is wrong.

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **The objective restated at every load of the feature steering file** | [`feature-steering.md`](../templates/feature-steering.md) | Periodic re-anchoring at the end of context, where attention is strongest |
| **Every built thing traces to a requirement number** | [`final-requirement.md`](../templates/final-requirement.md), traceability matrix | Untraceable work is detectable mechanically, not by feel |
| **Explicit out-of-scope lists** | raw requirement, requirement, design section | The highest-yield single line against scope creep |
| **Re-pin at every section boundary** | `A9` / `B9` | Drift accumulates across sections, not only across sessions |
| **Amendments, never silent requirement edits** | `A4` / `B4` | A silently changed requirement makes every prior review wrong (**F1**) |

---

## 6. Cascading error

### What it actually is

The most expensive failure in the taxonomy, because it invalidates work that was itself correct. One wrong
premise enters the context and everything built on it inherits the error — a design that is internally
flawless, an implementation that matches the design exactly, and all of it wrong because the survey said a
field was nullable when it was not.

**Correctness downstream of a wrong premise is worthless.** That is the property that makes it costly.

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **Fix at the source, then re-walk forward** | **F1** correction | Patching the symptom leaves the premise in place to poison the next thing |
| **Corrections log in the survey and review rounds** | [`relevant-info.md`](../templates/relevant-info.md), [`review-round.md`](../templates/review-round.md) | Makes the pattern of what gets wrong visible instead of burying it in a clean document |
| **`path:line` + read-and-verified on every claim** | grounding templates | Front-loads the verification where it is cheapest |
| **Grounding happens once, early, and in writing** | `A1` / `B1` | A written survey can be audited. Recollection cannot |
| **Amendment log on the requirement** | `A4` / `B4` | An edit with no record is a wrong premise with no trail |

---

## 7. Irreversible action and non-idempotent retry

### What it actually is

Retry is the default response to an error, and it is correct for pure operations. For anything with an
external effect it is a **second real event**: a second charge, a second email, a second increment of a
sequence.

The specific trap: an operation fails *after* its effect landed but *before* the confirmation returned. From
the inside, that is indistinguishable from an operation that never happened.

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **Classify reversibility before acting** | [`00-agent-contract.md`](../rules/00-agent-contract.md) §2 | The decision is made before the pressure of an error, not during it |
| **Hard stop on irreversible actions** | same, and the hard-stop list | Removes the judgement call entirely |
| **Confirm idempotency before any retry** | **F6** | Turns the default reflex into a checked step |
| **Add, verify, then remove — never the reverse** | [`60-workflow-and-verification.md`](../rules/60-workflow-and-verification.md) | Keeps a working path available at every intermediate state |
| **Removals go to a separate cutover step with per-item approval** | `A11` / `B10` | Feature momentum is exactly the wrong state of mind for a deletion |
| **Consumers found *and read*, with an unscoped search** | cutover pre-flight | The "impact-free removal" that was not |

---

## 8. Drift and conflicting instruction

### What it actually is

Rules get added; the ones they supersede rarely get removed. Two files then instruct differently, and the
model has no basis for choosing — so it picks one, silently, and possibly a different one next session.

This is a *documentation* failure that presents as an *agent* failure. The agent is following instructions.
There are two sets.

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **One authoritative statement per rule; cross-reference, never duplicate** | [`manifest.md`](../manifest.md) §4 | The only structural fix. Two copies always drift |
| **Adapters reference rule files, never copy them** | [`../adapters/README.md`](../adapters/README.md) | The most common source of drift in a multi-IDE setup |
| **Content hashes for any inlined content** | [`manifest.md`](../manifest.md) §4 | Makes drift mechanically detectable |
| **Stop and ask which wins** | **F11** | A silent choice leaves the conflict for the next session |
| **Delete, don't just add** | **F11** correction | Adding a correction beside a wrong rule creates the conflict |
| **Universal rules never edited in place** | [`65-auto-healing-docs.md`](../rules/65-auto-healing-docs.md) | Keeps re-sync safe, so upgrades do not resurrect old drift |

---

## Quick diagnostic

Something is wrong and you want the fastest route to the right playbook.

| What you observe | Start at | Code |
|------------------|----------|------|
| A claim turned out to be false | §1 hallucination | **F5**, or **F1** if inherited |
| A rule stopped being followed partway through | §2 context loss | **F4**, **F10** |
| The same fix attempted three times | §3 loops | **F3** |
| Lots of reading, nothing written | §3 loops | **F7**, **F12** |
| Declared done, wasn't | §4 sycophancy | **F9**, or **F8** |
| Building something nobody asked for | §5 goal drift | **F2** |
| A late finding invalidates earlier work | §6 cascading | **F1** |
| A duplicate external effect | §7 irreversibility | **F6** |
| Two rules disagree | §8 drift | **F11** |
| Guessed a shape and it broke | [`failure-taxonomy.md`](failure-taxonomy.md) | **F13** |
| Rules all present, none of them followed | §9 installed but not followed | — run the install audit |

---

## What this framework does not fix

Honesty is the point of this section. These remain open, and pretending otherwise is itself **F8**.

| Limitation | Why it survives | What reduces it |
|------------|-----------------|-----------------|
| Reading the right file and **misreading** it | grounding proves the file was opened, not understood | quote rather than paraphrase for 🔴 claims |
| A capped review round missing a real blocker | caps are hard by design, so a late finding cannot buy a round | front-loaded grounding; a 🔴 blocks the stage rather than being absorbed |
| A contract that exists only in a consumer's expectations | there is nothing to search for | ask the user what depends on it; treat the answer as a lead, then verify |
| Migration correctness without production-like data | cannot be verified locally | state the specific unverified condition as a residual risk; stage the rollout |
| Environment-only defects | local and production differ in ways no local run reveals | reproduce in the closest available build; name the layers still between |
| Untestable interactions — real device input, mail delivery, rendered styling | the test environment cannot produce them | a named manual check, honestly labelled |
| Process compliance itself | nothing forces the rules to be followed | the artefacts are the evidence; a missing artefact is visible |

The framework converts silent failures into visible ones. It does not make the failures impossible.

---

## 9. Installed but not followed

### What it actually is

The meta-failure. Every rule file is present and correct. The adapter exists. The agent can quote the tracks
back to you. And it still writes code without classifying the work, and still reads directories wide instead of
using the index.

This is not a comprehension failure and not a compliance failure. It is a **missing checkpoint**. An obligation
that is never *asked for* is not enforced by being written down more clearly, because the agent is not
disobeying a rule it consulted — it never reached the moment where consulting it was required.

The two most common reports are exactly the two most common missing checkpoints:

| Report | Actual cause |
|--------|--------------|
| "It did not follow the steps for a new feature" | nothing required the track to be stated before the first edit |
| "It missed implementing the indexing logic" | tier 0 was chosen and the choice produced no visible deliverable, so the work looked like an omission — or genuinely was one |

### The three-layer diagnostic

Work down. The first layer that fails is the cause; the layers below it are consequences.

| # | Layer | Question | Fails when | Fix |
|---|-------|----------|-----------|-----|
| 1 | **Loaded** | Is the rule in the agent's context at all? | it is in the ALWAYS set of the manifest but not named in the adapter | install-audit A4 |
| 2 | **Reachable** | Can the agent get to it from a cold start? | it is AUTO or ON-DEMAND and the adapter never names it, so the agent has never heard of it | install-audit A6 |
| 3 | **Required** | Is there a moment that makes the agent produce it? | there is no checkpoint — the rule is an obligation nobody asks for | [`../rules/01-session-preflight.md`](../rules/01-session-preflight.md) |

Layer 2 is where the track failure usually lives. `80-work-intake.md` is **AUTO**, and AUTO is unsupported on
most tools ([`../adapters/ide-matrix.md`](../adapters/ide-matrix.md)) — so unless the adapter names it
explicitly, work-intake silently never loads. The agent then has no classification step to skip, because it
does not know one exists.

Layer 3 is where it lives when layers 1 and 2 are fine. This was the framework's own gap until v1.1.0: the
process was fully described, fully reachable, and had no moment at which anything was checked.

### The guardrails

| Guardrail | Where | Why it works |
|-----------|-------|--------------|
| **The preflight block** | [`../rules/01-session-preflight.md`](../rules/01-session-preflight.md) | The one enforcement moment. Track, stage, gates, retrieval and pins must be stated before the first edit, so skipping them becomes visible rather than silent |
| **Install audit** | [`../setup/install-audit.md`](../setup/install-audit.md) | Behaviour probes ask questions; the audit opens files. An install can pass every probe with an empty index and an unfilled config |
| **Every tier has an index deliverable** | [`../retrieval/build-index.md`](../retrieval/build-index.md) | Removes "tier 0 means build nothing". Tier 0 *is* a hand-built index, and `S7` is not complete without it |
| **Report the tier and its limitation** | `build-index.md` §6 | Work that is invisible gets reported as missing. Two sentences prevent it |
| **Adapter content item 11** | [`../adapters/README.md`](../adapters/README.md) | The preflight must be named in the adapter, or the enforcement moment never loads either |
| **Behaviour checks 13 and 14** | [`../manifest.md`](../manifest.md) §3 | Probe indexing and the preflight directly — the two things the original twelve did not ask about |

### If it is happening to you now

1. Run [`../setup/install-audit.md`](../setup/install-audit.md). Report the per-block score.
2. Check rows **A4.8** (preflight named in the adapter), **A6.2** (work-intake reachable) and **A5.2**
   (`INDEX.md` Zone 2 populated). Those three cover almost every instance.
3. Fix the adapter, not the rules. The rules are almost never the problem — an unreferenced rule and a wrong
   rule look identical from the outside, and only one of them is common.
4. Build the index per [`../retrieval/build-index.md`](../retrieval/build-index.md) if A5.2 failed. Do **not**
   change tier to compensate; the tier was probably right and the deliverable was skipped.
5. Re-run `S8` and `S9`. Record both scores.

### Why the adapter, not the rules

An install that behaves wrongly is usually a *loading* problem wearing a *content* problem's clothes. Rewriting
a rule that was never in context produces a better rule that is still never in context — and now there are two
versions of it, which is **F11**.

Check whether the agent had the rule before deciding the rule is wrong.
