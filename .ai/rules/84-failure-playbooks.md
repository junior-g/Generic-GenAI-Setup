# Failure Playbooks

**Load mode: ON-DEMAND.** Pull in when a first move from [`05-failure-detection.md`](05-failure-detection.md)
did not clear the issue, or when two signals fire together and you need to tell them apart.

Each playbook is three parts: **containment** (stop the damage), **correction** (repair the state), and
**prevention** (the structural change that stops the recurrence). Taxonomy reference:
[`../knowledge/failure-taxonomy.md`](../knowledge/failure-taxonomy.md).

---

## Which one is it? — look-alike pairs

Symptoms overlap. Applying the wrong playbook wastes the attempt and usually makes the real cause harder to
see. These are the discriminating checks.

| What you see | Could be | Discriminating check |
|--------------|----------|---------------------|
| Output doesn't match the expected type or schema | **F13** or **F4** | Did it get cut off mid-structure? Truncation → F4. Complete but wrong shape → F13 |
| An operation produced a duplicate effect | **F6** or **F3** | Was there a deterministic key that should have prevented it? Missing key → F6. Same call repeated blindly → F3 |
| No progress for a long stretch | **F3** or **F7** | Same call + same error repeatedly → F3. Many different read-only calls and hedging language → F7 |
| Working on something unrelated to the request | **F2** or **F11** | Isolated to this long session → F2. Happening in fresh sessions too → F11 (a rule is instructing it) |
| Declared complete but the work isn't | **F9** or **F8** | A verification step was never run → F9. Jumped to agreement right after user input → F8 |
| A claim about the codebase turns out false | **F5** or **F1** | First-hand and never verified → F5. Inherited from an earlier wrong claim → F1, and fix the *source* |
| Budget gone with little shipped | **F12** or **F7** | Spent on retrieval and re-reading → F12. Spent on planning and review rounds → F7 |
| A rule seems to be ignored | **F10** or **F11** | It was stated early in a long session → F10. Another rule says the opposite → F11 |

---

## F1 — Cascading errors

**Signals.** A tool result contradicts an earlier claim. A later stage corrects an earlier document. A fix
requires changing something you wrote three steps ago.

**Root cause.** A wrong premise entered the context and everything downstream was built on it. The
downstream work may be individually correct and still worthless.

**Containment.** Stop forward work immediately. Do not patch the symptom — the symptom is downstream.

**Correction.** Find where the wrong claim **first** entered — usually the grounding survey or a gap
decision. Correct it *there*. Then walk forward through every artefact that cites it: requirement → design →
tasks → code. Record it in the survey's corrections log and the review round's corrections table.

**Prevention.** `path:line` pointers with a read-and-verified mark on every claim. Negatives get two checks
(F5). The corrections log exists precisely so the *pattern* of what gets wrong is visible rather than buried
in a clean-looking document.

## F2 — Goal drift

**Signals.** Building something the requirement does not ask for. Cannot recall the objective without
scrolling. The current section feels like the whole job.

**Root cause.** The original objective is thousands of tokens back, and recent specific context dominates
attention.

**Containment.** Stop. Re-pin the feature steering file and read the relevant requirement `R`-block.

**Correction.** Compare what you are building against the acceptance criteria. Anything not traceable to an
`R`-number is scope creep — remove it, or raise it as an explicit amendment.

**Prevention.** The per-feature steering file restates the objective and scope every time it loads. Re-pin at
every session start **and every section boundary**, not only at the beginning.

## F3 — Infinite failure loop

**Signals.** Two attempts failed the same way. Re-running a command hoping for a different result. Tweaking
one variable repeatedly.

**Root cause.** Your model of the failure is wrong, so each attempt tests the same wrong theory with
different parameters.

**Containment.** **Two-strike rule** — stop editing after the second identical failure.

**Correction.** Write down what the two failures **rule out**. That negative information is the useful
output. Then change approach at the level of the *theory*, not the parameters. On a bug fix this means
returning to the root-cause step and re-deriving it; log both attempts in the fix report's attempt log.

**Prevention.** Requiring a written root cause before the first edit means there is a theory to falsify
rather than a guess to tweak.

## F4 — Context overflow

**Signals.** Reading directories wide. Opening the same file a third time. Output truncated mid-structure.
Earlier instructions no longer influencing behaviour.

**Root cause.** Working context filled with material that could have been retrieved on demand.

**Containment.** Stop reading. Switch to a targeted query with the terms you expect in the source.

**Correction.** Pin the small set of files that actually matter in the feature steering file, and **prune
pins that are never read**. Move history out of context into artefacts — the status table and execution
reports exist so prior sections do not have to be re-read.

**Prevention.** Index-routed grounding once, early and narrowly. Lazy task elaboration so only the next
section is detailed. Per-feature pins instead of directory sweeps.

## F5 — Hallucination / weak grounding

**Signals.** About to assert what a file contains without having opened it. Relying on a search hit.
Concluding something is absent because a scoped search returned nothing.

**Root cause.** A pointer was treated as evidence.

**Containment.** Do not state it. Read the file.

**Correction.** Positive claims: read the file, cite `path:line`. Negative claims — "X doesn't exist",
"removing X is safe" — need **two independent checks by different methods**: an unscoped filename glob ·
reading the module or route table that would consume it · reading the write sites of the data · running the
relevant tests · checking the dependency manifest. A single scoped search is never sufficient.

**Prevention.** The negative-findings table in the grounding template, which will not accept one method. For
any 🔴 claim, quote the line rather than paraphrasing it — reading the right file and misreading it are
different failures, and the quote catches the second one.

## F6 — Irreversible or non-idempotent action

**Signals.** About to delete, drop, rename, or change access. Retrying an operation that already had an
external effect. A removal appearing inside feature work.

**Root cause.** Reversibility was never classified before acting.

**Containment.** **Stop and ask.** Do not proceed on your own judgement, however obvious it looks.

**Correction.** Classify: pure / reversible / compensable / irreversible. Irreversible actions need explicit
per-item approval and go in the cutover step, never inside a feature section. Before any retry, confirm the
operation is idempotent — check what the first call actually did before repeating it. An atomic increment, a
sent message, or a charge cannot be replayed safely.

**Prevention.** Add the new path, verify it, *then* remove the old one — never the reverse. Cutover pre-flight
requires every consumer to be found **and read**, with an unscoped search, plus a documented reversal path.

## F7 — Analysis paralysis / over-planning

**Signals.** Reviewing without producing findings. Wanting an extra round. Elaborating sections that are not
next. Hedging language accumulating. No file written and no gate run for a long stretch.

**Root cause.** Planning feels productive and carries no risk of being wrong yet.

**Containment.** Commit to the smallest concrete next action — one file, one test, one gate. Then do it.

**Correction.** Round caps are **hard, with no escape hatch**: Track A 3 / 3 / 2, Track B 2 / 2 / 1, B-lite
1 combined. At a cap the stage closes. 🟡 and 🟢 items become residual risks in the final review report. An
open 🔴 blocks the stage and is reported — it does not buy a round.

**Prevention.** Lazy task elaboration. A review round must emit findings *or* an explicit sign-off naming
files read, so a round cannot be spent producing nothing.

## F8 — Sycophancy

**Signals.** Agreeing with a stated assumption without checking it. A review that finds nothing and names
nothing. Softening a correction the user needs to hear. Confident phrasing standing in for verification.

**Root cause.** Agreement is cheaper than verification and feels cooperative.

**Containment.** Separate the claim from who made it, then check the claim.

**Correction.** State the correction plainly and continue. When the user's premise is wrong, say so — honest
feedback is more useful than agreement, and a wrong premise left standing costs a stage. A review round
outcome is either numbered findings or "no findings, verified by reading X, Y, Z".

**Prevention.** The raw-requirement templates state explicitly that the user's description of current
behaviour is *a claim to be verified*. Being wrong there is framed as useful, which removes the social cost
of contradicting it.

## F9 — Amnesia / premature termination

**Signals.** About to say done with follow-ups open or a gate unrun. Resuming from memory. Losing track of
which section is in progress.

**Root cause.** Completion state was held in context rather than externalised.

**Containment.** Do not report completion. Check the status table and the follow-ups list.

**Correction.** A section is not complete while any follow-up is open. Run all four gates and paste the real
output. On resume, read the status table and the latest execution report **first** — artefacts are the resume
point, not memory and not the conversation.

**Prevention.** The status table, per-section execution reports, and the rule that follow-ups block closure
are the externalised state machine. They work because they are outside the context window.

## F10 — Positional attention bias

**Signals.** A constraint stated early in a long session no longer influencing output. Rules from the middle
of a long document being missed.

**Root cause.** Attention degrades for material in the middle of a long context.

**Containment.** Re-state the constraint at the **end** of the working context, where it will be attended to.

**Correction.** Re-pin the feature steering file — it is short and carries exactly the constraints that
matter. For long documents, work from the artefact's own summary tables rather than re-reading prose.

**Prevention.** Immutable rules live in short always-on files. Transient instructions get restated at section
boundaries. This is also why the eleven non-negotiables are inlined in `MAIN.md` rather than referenced.

## F11 — Drift and conflicting instruction

**Signals.** Two rules or documents instructing differently. A document stating something the code
contradicts. Guidance that no longer matches how the project works.

**Root cause.** Instructions were added without retiring what they superseded.

**Containment.** **Stop. Report the conflict and ask which wins.** Do not silently pick one — a silent choice
means the conflict stays for the next session, and the next one may pick differently.

**Correction.** Fix the stale source, and **delete rather than merely add**. Then sweep for every other file
that states the same rule.

**Prevention.** One authoritative statement per rule, cross-referenced rather than duplicated. That is why
adapters reference rule files instead of copying them, and why inlined content in an adapter must carry a
content hash. [`70-do-not-regress.md`](70-do-not-regress.md) fences the specific rules that have already
drifted once.

## F12 — Credit and resource exhaustion

**Signals.** Session well past its declared budget. Retrieval dominating the work. Re-reading the same
material. Elaborating work that is not next.

**Root cause.** No budget was declared, or no checkpoint against it.

**Containment.** Report progress and remaining budget. Propose phasing rather than continuing silently.

**Correction.** The levers, highest saving first — full model in
[`../knowledge/cost-model.md`](../knowledge/cost-model.md):

1. Hard round caps.
2. A properly filled raw requirement — every pre-answered question is a round avoided.
3. Per-feature context pins.
4. Lazy task elaboration.
5. Index-first retrieval with no directory sweeps.
6. Track C for bugs, so a defect never enters the Track A/B machinery.

**Prevention.** Declare a session budget in the raw requirement and check against it at every stage boundary.

## F13 — Schema and type breakage

**Signals.** Guessing a response shape. An escape-hatch type creeping in. Static analysis failing on a shape
you assumed. Output not matching the expected structure.

**Root cause.** The contract was inferred instead of read.

**Containment.** Read the declaration before writing the value.

**Correction.** Fix the *type* when the type is wrong, not the call site. Never widen to an escape-hatch type
to make a gate pass — that converts a caught failure into an uncaught one.

**Prevention.** Types in their declared home, validated at every boundary, with the static gate in the
definition of done.

---

## Choosing a response by severity

Reversibility decides how much caution, not how much effort.

| Class | Examples | Response |
|-------|----------|----------|
| **Pure** — no world effect | reading, searching, running gates | Fix and continue. No need to report unless it changed a conclusion |
| **Reversible** — local, undoable | source edits, a new route, a new table | Fix, continue, note it in the execution report |
| **Compensable** — needs a deliberate undo | schema change with a migration, feature-flagged behaviour, a backfill | Stop, state the compensation, then proceed |
| **Irreversible** — cannot be undone | dropping live data, deleting a live route, permission changes, mail, money | **Hard stop. Ask.** Route to cutover with per-item approval |

Two rules override the table:

- After **two identical failures**, stop and change approach (**F3**), regardless of class.
- An open 🔴 at a round cap **blocks the stage** and is reported rather than absorbed (**F7**).

## When two playbooks both seem to apply

Run the containment step of the **more severe** one first — containment is cheap and rarely wrong. Then use
the look-alike table to pick which correction to apply. Applying two corrections at once makes it impossible
to tell which one worked, which is how a fixed problem becomes an unexplained one.
