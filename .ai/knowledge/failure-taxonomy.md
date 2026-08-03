# Failure Taxonomy — `F1` to `F13`

**Load mode: ON-DEMAND.** The reference definition of each code. Use it when you need the precise boundary of
a code, or to tell two apart.

| Where to go | For |
|-------------|-----|
| [`../rules/05-failure-detection.md`](../rules/05-failure-detection.md) | the runtime signal → first-move table |
| [`../rules/84-failure-playbooks.md`](../rules/84-failure-playbooks.md) | containment, correction, prevention |
| [`ai_troubleshooting.md`](ai_troubleshooting.md) | why the limitation exists at all |

The codes are **stable across every project** using this framework, which is what lets a fix report from one
codebase be read by someone working on another. They derive from published research on production LLM-agent
failure modes, restated in terms of the decisions an agent actually makes on a codebase.

---

## Index

| Code | Name | Class | Most often caught by |
|------|------|-------|---------------------|
| **F1** | Cascading error | grounding | a later stage contradicting an earlier document |
| **F2** | Goal drift | attention | work that traces to no requirement |
| **F3** | Infinite failure loop | planning | the two-strike count |
| **F4** | Context overflow | attention | repeated reads, truncated output |
| **F5** | Hallucination / weak grounding | grounding | an unverified claim in review |
| **F6** | Irreversible or non-idempotent action | side effect | the reversibility classification |
| **F7** | Analysis paralysis / over-planning | planning | a round cap |
| **F8** | Sycophancy | alignment | a review that names nothing |
| **F9** | Amnesia / premature termination | state | an open follow-up at closure |
| **F10** | Positional attention bias | attention | a constraint no longer applied |
| **F11** | Drift and conflicting instruction | consistency | two documents disagreeing |
| **F12** | Credit / resource exhaustion | resource | the session budget |
| **F13** | Schema and type breakage | contract | the static gate |

Five classes: **grounding** (is it true), **attention** (is it still in view), **planning** (is progress being
made), **state** (is completion real), and three singletons — **side effect**, **alignment**, **consistency**,
**contract**, **resource**.

---

## F1 — Cascading error

**Definition.** A wrong premise entered the working context and subsequent work was built on it. The
downstream work may be individually correct and is still invalid.

**Observable signature.** A tool result contradicts something already claimed. A later stage corrects an
earlier document. A fix requires changing something written three steps ago.

**Diagnostic.** Trace the claim backwards. If it originated in *this* reasoning step and was never verified,
it is **F5**. If it was inherited from an earlier artefact, it is **F1** and the fix belongs at the origin.

**Severity.** Highest cost in the taxonomy — it invalidates correct work, and the amount invalidated grows
with how long the premise survived.

**Boundary.** F5 is the *first* wrong claim. F1 is everything built on it.

## F2 — Goal drift

**Definition.** Measurable deviation from the original objective under context length and competing local
sub-goals. The agent quietly redefines "done" as the task currently in hand.

**Observable signature.** Building something the requirement does not ask for. Cannot state the objective
without scrolling. The current section feels like the whole job.

**Diagnostic.** Take what is being built and try to trace it to a requirement number. No trace means drift.
If it also happens in *fresh* sessions, it is not drift — a rule is instructing it, which is **F11**.

**Severity.** Moderate and compounding. Each drifted section makes the next one drift further, because it
becomes the local context.

**Boundary.** F2 is attention decay within a session. F10 is attention decay by *position*. F2 is about the
objective; F10 is about any constraint.

## F3 — Infinite failure loop

**Definition.** Repeated attempts at a failing action without changing the underlying theory. Each attempt
varies parameters, not the model of the failure.

**Observable signature.** Two attempts failed the same way. Re-running a command hoping for a different
result. One variable tweaked repeatedly.

**Diagnostic.** Same call plus same error → F3. Many *different* read-only calls with no commitment → **F7**.

**Severity.** High resource cost, zero forward progress, and it consumes the budget that the correct approach
would have needed.

**Boundary.** F3 is repetition. F7 is avoidance. Both look like "no progress".

## F4 — Context overflow

**Definition.** Accumulated context exceeds what can be attended to or retained, so instructions and material
are silently dropped.

**Observable signature.** Reading directories wide. The same file opened a third time. Output truncated
mid-structure. Earlier instructions no longer influencing behaviour.

**Diagnostic.** Output cut off mid-structure → F4. Complete but structurally wrong → **F13**. Instructions
ignored *from the middle* of a long document rather than the start → **F10**.

**Severity.** Moderate, and it is the enabling condition for F2, F9 and F10. Fixing overflow often fixes
three symptoms.

**Boundary.** F4 is capacity. F10 is position within capacity. F12 is cost.

## F5 — Hallucination / weak grounding

**Definition.** Asserting something about the codebase that was not read. Includes generated identifiers,
paths and configuration keys, and — most dangerously — **confident negatives**.

**Observable signature.** A claim with no `path:line`. A conclusion resting on a search hit. "X does not
exist" after one scoped search.

**Diagnostic.** Ask: *did I open the file this session?* If no, F5. If the claim was inherited, **F1**.

**Severity.** High, and highest for negatives. A wrong positive usually fails a gate. A wrong negative
produces coherent work built on absence, and no gate checks absence.

**Boundary.** F5 is a claim about what *is*. F13 is a claim about a *shape*. Both are guessing; they fail
differently.

## F6 — Irreversible or non-idempotent action

**Definition.** Taking an action whose external effect cannot be undone, without classification or approval.
Includes retrying an operation whose first attempt already had an effect.

**Observable signature.** About to delete, drop, rename, or change access. Retrying after an external effect.
A removal appearing inside feature work.

**Diagnostic.** Was there a deterministic key that should have prevented the duplicate? Missing → F6. Same
call repeated blindly with no external effect → **F3**.

**Severity.** Critical. The only class where the failure is not recoverable by better subsequent work.

**Boundary.** F6 is about the world outside the process. Everything else is recoverable inside it.

## F7 — Analysis paralysis / over-planning

**Definition.** Expending resources on planning, reviewing and validating while making minimal progress.
Planning is safe because it cannot yet be wrong.

**Observable signature.** Reviewing without producing findings. Wanting an extra round. Elaborating sections
that are not next. Accumulating hedging language. No file written, no gate run.

**Diagnostic.** Ratio of planning to execution across the session. Many diverse read-only calls with hedging
→ F7. Same call repeatedly → **F3**.

**Severity.** Moderate, and historically the **largest avoidable share** of session cost — concentrated in
review rounds.

**Boundary.** F7 is spending on thinking. F12 is spending on reading. Both exhaust the budget.

## F8 — Sycophancy

**Definition.** Prioritising agreement with the user's framing over correctness. Includes reviews that find
nothing, unchecked assumptions, and softened corrections.

**Observable signature.** Accepting a stated assumption without checking. A review naming no files. Confident
phrasing standing in for verification. Agreement immediately following user input.

**Diagnostic.** Was a verification step *run*, or was agreement substituted for it? Never run → F8. Run and
still incomplete → **F9**.

**Severity.** Moderate, but it disables the other controls — a review round that finds nothing has consumed a
round and provided no assurance.

**Boundary.** F8 is agreeing without checking. F9 is stopping without finishing.

## F9 — Amnesia / premature termination

**Definition.** Declaring completion when the objective is not met, because completion state was held in
context rather than externalised.

**Observable signature.** About to report done with a follow-up open or a gate unrun. Resuming from memory.
Losing track of which section is in progress.

**Diagnostic.** Check the status table and the follow-up list. A verification step never run → F9. Jumped to
agreement after user input → **F8**.

**Severity.** High, because it terminates the process that would have caught everything else.

**Boundary.** F9 is stopping too early. F2 is going somewhere else.

## F10 — Positional attention bias

**Definition.** Degraded use of information located in the middle of a long context. Constraints not anchored
near the beginning or the end are silently under-weighted.

**Observable signature.** A constraint stated early in a long session no longer influencing output. Rules
from the middle of a long document missed while its opening and closing rules are followed.

**Diagnostic.** Where in the context is the ignored rule? Middle → F10. Beyond the window entirely → **F4**.
Contradicted by another rule → **F11**.

**Severity.** Moderate and invisible. There is no error and no symptom other than the missing behaviour.

**Boundary.** F4 is "no longer present". F10 is "present but not attended to".

## F11 — Drift and conflicting instruction

**Definition.** Two sources instructing differently, or an instruction that no longer matches reality.
Instructions were added without retiring what they superseded.

**Observable signature.** Two rules or documents disagreeing. A document contradicted by the code. Guidance
that no longer matches how the project works.

**Diagnostic.** Does the wrong behaviour reproduce in a *fresh* session? Yes → F11, something is instructing
it. Only in long sessions → **F2** or **F10**.

**Severity.** Moderate per instance, high cumulatively. Each unresolved conflict means the behaviour is
non-deterministic across sessions.

**Boundary.** F11 is a defect in the *instructions*. F2 and F10 are defects in *attention* to correct
instructions.

## F12 — Credit / resource exhaustion

**Definition.** Consuming the session's budget without a checkpoint against it, typically on retrieval and
re-reading.

**Observable signature.** Session well past its declared budget. Retrieval dominating the work. Re-reading
the same material. Elaborating work that is not next.

**Diagnostic.** Where did the spend go? Retrieval and re-reading → F12. Planning and review rounds → **F7**.

**Severity.** Moderate. It converts a completable task into a partial one, and partial work carries the cost
of a handover.

**Boundary.** F12 is resource. F4 is capacity. They co-occur: wide reading causes both.

## F13 — Schema and type breakage

**Definition.** Producing output that violates an expected type, schema, or interface, because the contract
was inferred instead of read.

**Observable signature.** Guessing a response shape. An escape-hatch type appearing. The static gate failing
on an assumed shape. Output not matching the expected structure.

**Diagnostic.** Complete but wrong shape → F13. Cut off mid-structure → **F4**.

**Severity.** Low cost when the static gate catches it, which is usually. High when it is "fixed" by widening
the type — that converts a caught failure into an uncaught one, and is the reason this has its own code.

**Boundary.** F13 is a guessed *shape*. F5 is a guessed *fact*.

---

## Using the codes

**When a signal fires and changes what you do**, name it in one line: code, signal, move.

> `F4` — third read of the same handler. Pinned it in the feature steering file instead of re-reading.

**Log it** in the execution report's discoveries section or the fix report's attempt log. The point is not
bookkeeping: a project that hits **F5** four times in one feature has a grounding problem, and only the record
makes that visible.

**Do not use a code to explain away a mistake.** "That was F5" is a diagnosis, not an excuse — it obliges the
correction and the prevention step, not just the label.

**When two codes both fit**, apply the containment step of the more severe one first (containment is cheap and
rarely wrong), then use the look-alike table in
[`../rules/84-failure-playbooks.md`](../rules/84-failure-playbooks.md) to choose the correction. Applying two
corrections at once makes it impossible to tell which one worked.
