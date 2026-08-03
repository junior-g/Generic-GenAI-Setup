# Rationale

Why the framework is shaped this way. Every rule in it has a cost — a document to write, a check to run, a
round it forbids — so every rule should be able to name the failure it prevents.

This file does that, rule by rule. It is also the honest record of what the framework **does not** fix.

> **Provenance.** This is a generalisation of a working setup, not a design exercise. The process rules come
> from session-level post-mortems on a real codebase, where each was added in response to a specific defect.
> The `F1`–`F13` taxonomy is grounded in published research on production LLM-agent failure modes. What was
> project-specific has been stripped; what was structural has been kept.

---

## 1. The central claim

**An AI agent's failures on a large codebase are structural, not attitudinal.** They do not come from
carelessness and they are not fixed by a better prompt. They follow from five properties of the machine:

| # | Property | What follows |
|---|----------|--------------|
| 1 | It predicts plausible text; there is no separate faculty that knows what is true | a fluent wrong answer costs the same to produce as a right one, and reads identically |
| 2 | Context is finite, and attention within it is uneven | a constraint given early stops working, silently, with no error |
| 3 | No memory persists between sessions | anything not written down is gone; confidence about "what we decided" is reconstruction |
| 4 | No execution model of the code, unless something runs it | "this should work" is a prediction, not evidence |
| 5 | Training rewards helpfulness and agreement, not correctness under disagreement | agreeing is the path of least resistance and feels cooperative |

Every rule in the framework traces to one of these. The full mechanism per failure is in
[`../.ai/knowledge/ai_troubleshooting.md`](../.ai/knowledge/ai_troubleshooting.md).

## 2. The five structural moves

Given those properties, five design moves do most of the work.

### 2.1 Externalise state

**Because of property 3.** Completion state held in context is lost at a session boundary or a compaction, and
what replaces it is reconstruction — which is invention with a confident tone.

So: a status table, per-section execution reports, a follow-up list that blocks closure, and a declared resume
point. These work *because they are outside the context window*.

The test of whether this is real: on "continue", the artefacts are read first, before memory and before the
conversation. A framework that says "resume from the artefacts" but is never checked has not externalised
anything.

### 2.2 Make evidence a requirement, not a virtue

**Because of property 1.** There is no internal signal distinguishing "I read this" from "this is what a
codebase like this would contain". So the standard cannot be introspective — it has to be mechanical.

So: every claim carries `path:line` and a read-verified mark, and **a negative claim needs two independent
checks by different methods.**

The asymmetry between positives and negatives is the important part. A wrong positive usually fails a gate
within minutes. A wrong negative — "this doesn't exist", "nothing depends on this", "removing it is safe" —
produces confident work built on absence, and **no gate checks absence**.

That is not theoretical. The two most expensive wrong claims in this framework's history were both negatives,
both from a single scoped search, and both survived two review stages before being caught.

### 2.3 Cap the stages, with no escape hatch

**Because of property 5 plus the safety of planning.** Planning cannot yet be wrong, so it is the comfortable
place to spend a session. Left uncapped, review rounds expand to fill whatever budget exists.

So: Track A 3/3/2, Track B 2/2/1, and **no exception clause**.

The absence of the escape hatch is the design, not an oversight. A cap with an exception is a suggestion, and
**F7** finds the exception every time. What makes a hard cap honest rather than a way of hiding open questions
is that unresolved items become *recorded residual risks* in the final review report — named, with the default
applied and the risk if it is wrong.

### 2.4 Classify before acting

Two classifications, at different scales.

**Work type**, because a bug fix entering the new-feature machinery acquires gap rounds, a design stage and two
review stages it does not need — while a feature done as a bug fix skips the grounding and the contract
register and pays for it at execution.

**Reversibility**, because retry is the correct default for pure operations and catastrophic for anything with
an external effect. Classifying *before* acting means the decision is made calmly rather than under the
pressure of an error.

### 2.5 Name the failures

**Because a failure you cannot name is one you improvise around.** `F1`–`F13` give a fixed vocabulary, a first
move per signal, and a remediation playbook.

The codes are stable across projects, which is what lets a fix report from one codebase be read by someone
working on another. And logging them makes the *pattern* visible: a project that hits **F5** four times in one
feature has a grounding problem, and only the record reveals it.

---

## 3. Rule by rule

### The grounding rule

| | |
|---|---|
| **Rule** | A pointer is never evidence. Negatives need two independent checks by different methods |
| **Prevents** | **F5** hallucination, **F1** cascading error |
| **Costs** | one file read per claim; one extra check per negative |
| **Evidence it is needed** | two "removing this is impact-free" claims, both wrong, both scoped-search negatives, both survived two review stages |
| **Verdict** | the highest-return rule in the framework |

### The four gates

| | |
|---|---|
| **Rule** | LINT, STATIC, BUILD, TEST — all four, real output, every time |
| **Prevents** | **F5** (an invented API fails the static gate), **F9** premature completion |
| **Costs** | minutes per section |
| **Why "real output"** | "gates pass" is unfalsifiable; pasted output is not. The claim and the evidence are different artefacts |
| **Why never weakened** | a suppression, a widened type, or an excluded file converts a *caught* failure into an *uncaught* one — strictly worse than the original failure |
| **Verdict** | the cheapest verification available. Skipping one trades minutes for a debugging session |

### Gates as roles, not commands

| | |
|---|---|
| **Rule** | Rules refer to LINT / STATIC / BUILD / TEST; only `config.yml` knows the command |
| **Enables** | language independence — the entire mechanism of it |
| **Costs** | one indirection |
| **Design note** | when a language has no type checker, the gate is *substituted and declared*, never deleted. Four gates always exist; sometimes one stands in for another |

### Hard round caps

| | |
|---|---|
| **Rule** | A: 3/3/2 · B: 2/2/1 · B-lite: 1 combined. No escape hatch |
| **Prevents** | **F7** paralysis, **F12** exhaustion |
| **Costs** | some genuine findings arrive after the cap |
| **Compensation** | front-loaded grounding, the runtime-feasibility check, and a 🔴 **blocking** the stage rather than being absorbed |
| **Verdict** | worth it. Review rounds are the largest avoidable share of session cost |

### The three tracks

| | |
|---|---|
| **Rule** | Classify A/B/C/T before starting; the user states the track |
| **Prevents** | **F12** — a defect costing what a feature costs |
| **Design note** | Track B's distinguishing artefact is `do-not-break.md`; Track C's is the written root cause before the first edit. Each track's unique control is the thing its failure mode demands |

### `do-not-break.md`

| | |
|---|---|
| **Rule** | Mandatory on Track B, including B-lite. Contracts, consumers, and the check that proves each still holds |
| **Prevents** | the unknown-consumer failure — the defining Track B risk |
| **Costs** | a document, and reading every consumer |
| **Why "read, not searched"** | a search finds consumers that use the name you searched for. It does not find the one reaching the data another way |
| **Why not frozen at `B1`** | the `B6` regression sweep appends to it; so does execution. A register closed early is a register that missed something |

### Root cause before the first edit

| | |
|---|---|
| **Rule** | Mechanism, exact `path:line`, and why it manifests only under the broken condition — in writing, before any code changes |
| **Prevents** | **F3** the fix loop, **F5** speculative edits to working code |
| **Enforcement mechanism** | the fix report exists before the fix does. It cannot be satisfied retroactively — that ordering *is* the gate |
| **The contrast check** | a cause that does not explain why the working condition works is not yet the cause. This is what catches plausible-but-wrong causes, and it is the part most often skipped |

### Fails-before / passes-after

| | |
|---|---|
| **Rule** | A regression test must be observed failing, then observed passing. Both pasted |
| **Prevents** | verification theatre |
| **Reasoning** | a test written after the fix that has never been red proves only that the code does what the code does |

### Two strikes

| | |
|---|---|
| **Rule** | Two failures of the same approach ends the approach |
| **Prevents** | **F3** |
| **Why a count, not judgement** | "am I making progress?" is exactly the question a loop answers wrongly. A count is checkable without the judgement that is impaired |
| **The real output** | not a third attempt — the set of explanations the two failures *rule out*. That set only exists if it is written down |

### Cutover as a separate step

| | |
|---|---|
| **Rule** | Removals never ride inside feature work. Separate step, per-item approval, consumers read, unscoped search, written reversal path |
| **Prevents** | **F6** |
| **Costs** | a stage |
| **Reasoning** | feature momentum is exactly the wrong state of mind for a deletion. And add-verify-remove keeps a working path at every intermediate state, which remove-then-add does not |
| **Verdict** | worth it — the failure it prevents is the one class that better subsequent work cannot recover |

### 3× verification

| | |
|---|---|
| **Rule** | Three passes: logic, structure, instruction-adherence. A failed pass means discard and regenerate |
| **Prevents** | **F8** confidence substituting for verification, **F9** |
| **Why three and not "check carefully"** | re-reading with the same question produces the same answer. The passes ask *different* questions in a deliberate order |
| **Why order matters** | structure is cheap but meaningless on broken logic; instruction-adherence decides whether the work should exist at all, and finding that out last is cheaper than the reverse |
| **Why discard, not patch** | a structurally wrong artefact patched at the surface is still wrong underneath — and patching it is the fix loop in document form |
| **Proportionality** | three passes on a one-line constant change is theatre. The rule scales, and says so |

### Auto-healing documentation

| | |
|---|---|
| **Rule** | A document found missing, incomplete, or contradicted pauses the task: fix it, index it, resume |
| **Prevents** | **F1** — a wrong document walked past is a wrong premise everything downstream inherits |
| **Why immediate** | it triggers on an observation, not a step, so it cannot be scheduled. There is no documentation phase |
| **Why bounded** | depth 1, proportionate, never mid-edit. Without limits it consumes the session — which is **F7** wearing a helpful expression |
| **Code wins** | a document describes; the code *is*. Unless the code contradicts an explicit requirement, in which case the code is the defect and gets raised, not documented as intended |

### Reference, never copy

| | |
|---|---|
| **Rule** | Adapters point at rule files. Inlined content carries a content hash |
| **Prevents** | **F11** |
| **Reasoning** | two copies of a rule drift — always, and usually within weeks. Then two sessions behave differently on the same question with no way to tell which is right |
| **The exception** | the eleven non-negotiables, inlined in `MAIN.md`. Short, stable, and missing one costs more than the drift risk. Stated as an exception rather than left to look like an inconsistency |

### Universal rules never edited in place

| | |
|---|---|
| **Rule** | `rules/00`–`95` copied verbatim; project content lives in `rules/project/` |
| **Enables** | safe re-sync when the framework is upgraded |
| **Reasoning** | if the universal layer is edited per project, an upgrade either overwrites local fixes or cannot happen. The split is what makes the framework maintainable across many installations |

### Twelve behaviour checks

| | |
|---|---|
| **Rule** | Activation is not complete until each is demonstrably true |
| **Prevents** | **F8**, **F9** at setup time — the install that claims success without evidence |
| **Reasoning** | reproducing files is not the goal; reproducing *behaviour* is. A file that exists and is never loaded has changed nothing |
| **Why the honest score matters** | a recorded 12/14 with named gaps is an install someone can improve. A claimed 14/14 is one nobody can |

---

## 4. Deliberate omissions

Things considered and left out, with the reason. Omissions are decisions and deserve the same treatment as
inclusions.

| Not included | Why |
|--------------|-----|
| A bundled retrieval engine | it would need a language, a runtime and dependencies — exactly what a portable framework cannot assume. The tier decision and the build spec are provided instead |
| Token or currency figures in the cost model | rates and models change; a fabricated number is worse than none (**F5**). The *shape* of the spend and the *ranking* of the levers are stable, so those are given |
| Prescribed libraries or tools in the task formats | a format that assumes a framework stops being portable. Concrete choices live in the generated project layer |
| A fifth track for refactoring | a refactor changes existing behaviour under existing contracts, which is Track B. A fifth track would duplicate it and drift |
| Automatic escalation when a cap is hit | that *is* the escape hatch, wearing a process costume |
| Rules for prompt phrasing or model selection | they change per release and per vendor. Everything here is about structure, which does not |
| A machine-readable rule schema | plain Markdown is readable by every assistant with no parser. A schema would buy validation and cost universality |
| Enforcement tooling | the artefacts *are* the evidence; a missing artefact is visible. A linter for process compliance is a project of its own, and would still not make the rules be followed |

## 5. What this framework does not fix

The honest list. Claiming otherwise would itself be **F8**.

| Limitation | Why it survives | What reduces it |
|------------|-----------------|-----------------|
| Reading the right file and **misreading** it | grounding proves the file was opened, not understood | quote rather than paraphrase for any 🔴 claim |
| A capped review round missing a real blocker | caps are hard by design, so a late finding cannot buy a round | front-loaded grounding; a 🔴 blocks the stage rather than being absorbed |
| A contract existing only in a consumer's expectations | there is nothing to search for | ask the user what depends on it; treat the answer as a lead, then verify |
| Migration correctness without production-like data | cannot be verified locally, at all | state the specific unverified condition as a residual risk; stage the rollout |
| Environment-only defects | local and production differ in ways no local run reveals | reproduce in the closest available build; name the layers still between |
| Untestable interactions — device input, real delivery, rendered appearance | the test environment cannot produce them | a named manual check, honestly labelled as manual |
| Requirement churn after design begins | a late `R`-change invalidates design and tasks | design starts only after approval; later changes are logged amendments with an impact note |
| Process compliance itself | nothing forces the rules to be followed | the artefacts are the evidence; a missing one is visible |

**The framework converts silent failures into visible ones. It does not make the failures impossible.** That
distinction is the whole of what it claims.

## 6. Revisiting this

The framework is versioned in [`CHANGELOG.md`](CHANGELOG.md), and every installation records the version it
came from in `config.yml`.

Worth revisiting when:

| Trigger | Look at |
|---------|---------|
| A failure mode recurs that no `F`-code covers | add `F14`, with the signal and the first move |
| A cap is hit repeatedly on the same stage | the cap may be wrong, or the stage before it is under-invested |
| A rule is routinely worked around | either it is wrong, or its cost is not being paid for a reason worth understanding |
| A template section is always `Not applicable` | it belongs in a different template, or nowhere |
| A behaviour check fails on several tools | the probe may be unclear, or the manifest's expectation unrealistic |
| An install repeatedly needs inlined content | note it in the IDE matrix; that tool may not be able to follow pointers at all |

Changing a rule means editing it here, bumping the version, and recording in the changelog whether existing
installations need to re-sync. A rule changed without a version bump is drift in the framework itself — the
same **F11** it exists to prevent.

---

## 7. The enforcement layer — added in 1.1.0

v1.0.0 had a structural hole that took two real installs to expose: **it described the process completely and
never checked it.**

Both installs produced a correct rule set, a correct adapter, and an agent that could quote the tracks back on
request. Both then wrote code without classifying the work, and neither built an index. Both would have scored
full marks on v1.0.0's twelve-check set, because none of those twelve asked about either thing.

That is not a compliance failure. Every rule in the framework was an *obligation*, and there was no moment in a
session or an install at which anything had to be *produced*. An obligation nobody asks for is not enforced by
being written more clearly.

### The session preflight

| | |
|---|---|
| **Rule** | Before the first file edit of any session, emit five lines: track, stage and resume read, gates, retrieval tier, pins |
| **Prevents** | **F2** drift, **F9** resuming from memory, **F10** early constraints falling out of view, **F4** the reflex to read directories wide, **F12** no budget checkpoint |
| **Costs** | seconds, once per session and once per section boundary |
| **Why it works** | it is the only place the process is *asked for*. It also lands at the end of the working context, immediately before the work, which is where a constraint is actually attended to |
| **Why it is a stop, not a formality** | three of the five lines can halt the work — an unclassified track, gates that cannot be filled from `config.yml`, an absent index. A checkpoint that cannot fail is a comment |
| **Verdict** | the highest return per line in the framework. It is nine lines of obligation made visible in five |

### The install audit

| | |
|---|---|
| **Rule** | Activation step `S9`: 69 rows across 7 blocks, each verified by **opening the file** |
| **Prevents** | **F8** and **F9** at install time — the setup that reports success without checking its own output |
| **Why separate from `S8`** | behaviour probes ask questions; the audit opens files. They fail *differently*, and merging them is precisely how an install passes every behaviour probe with an empty index and a `config.yml` full of unreplaced placeholders |
| **Why four blocking rows** | an incomplete `config.yml` means no definition of done; an unreferenced preflight means the process will not be followed; an empty `INDEX.md` means every task starts with a directory sweep; an unreachable work-intake means work is never classified. Everything else degrades, these four break it |
| **Verdict** | the counterpart the behaviour checks were always missing |

### Every tier has an index deliverable

| | |
|---|---|
| **Rule** | `S7` produces an index at every tier. Tier 0 *is* a hand-built index, not the absence of one |
| **Prevents** | the most common activation failure — a tier chosen and nothing built |
| **What was wrong in 1.0.0** | `index-spec.md` §2 correctly steers most projects to tier 0, and §3 then described only how to *query*. There was no build step, so "no engine" read as "no indexing", and the correct decision produced no visible artefact |
| **The second half of the fix** | `S7` must report the tier **and its limitation** to the user in two sentences. Work that is invisible gets reported as missing, and that is a support round caused by a missing sentence |
| **Verdict** | the framework was right about tiers and silent about deliverables. Being right silently is indistinguishable from being wrong |

### What this says about the framework's own design

The three additions have one shape in common, and it is the same shape as the rules they sit beside:
**convert a silent failure into a visible one.**

The grounding rule does not make an agent read files; it makes an unread file visible as a missing pointer. The
gates do not make code correct; they make incorrectness visible before it ships. The preflight does not make an
agent follow a track; it makes not following one visible in the first five lines.

The v1.0.0 gap was that this principle had been applied to the *work* and not to the *framework*. An installed
framework that was not being followed produced no signal at all — which, by its own standard, is the one failure
mode it should never have shipped with.
