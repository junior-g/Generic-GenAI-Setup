# Auto-Healing Documentation

**Load mode: ALWAYS.** This rule triggers on an *observation*, not on a step, so it cannot be scheduled and
it cannot be deferred to a documentation phase. There is no documentation phase.

---

## The rule

> The moment you find a document that is **missing**, **incomplete**, or **contradicted by the code**:
>
> 1. **Pause** the current task, mid-step, wherever you are.
> 2. **Create or correct** the document.
> 3. **Update** [`../INDEX.md`](../INDEX.md) so it reflects the change.
> 4. **Then resume** the original task from where you paused.

Three steps, in that order, before any further forward work. Not "at the end". Not "in a follow-up". The
reason is mechanical: a wrong document you walked past is a wrong premise that everything downstream inherits
— that is **F1**, cascading error, and it is the most expensive failure in the taxonomy because it
invalidates work that was itself correct.

## What triggers it

| Trigger | Example |
|---------|---------|
| **Missing** | A component, endpoint, collection or flow that no document describes, and which you had to reverse-engineer to proceed |
| **Incomplete** | A document that covers the happy path but not the states, errors, or edge cases you now need |
| **Contradicted** | The document says the flag defaults to off; the code defaults it on |
| **Stale** | The document describes a module, path, command or version that no longer exists |
| **Superseded** | Two documents describe the same thing differently and one is clearly older (**F11**) |
| **Unreachable** | A real document that [`../INDEX.md`](../INDEX.md) does not list, so nobody will find it |
| **Phantom** | An `INDEX.md` row pointing at a document that does not exist |

The trigger is *you needed it and it let you down*. A document you did not read and do not need is not
triggered — auto-healing is not a licence to audit the whole corpus (**F7**).

## The healing action, per trigger

| Trigger | Action | Scope limit |
|---------|--------|-------------|
| Missing | Write it, covering what you had to establish to proceed | What you learned. Not the whole subsystem |
| Incomplete | Add the missing part | The gap that blocked you |
| Contradicted | **Code wins.** Correct the document to match reality, and note the correction | If the *code* looks wrong, that is a bug — raise it, do not "fix" the doc to match a defect |
| Stale | Update the path/command/version, and delete what no longer exists | Deleting stale *content* is fine. Deleting a whole document is a removal — ask first (**F6**) |
| Superseded | Fix the authoritative one, retire the other, cross-reference | Retiring means marking superseded and pointing forward, not silent deletion |
| Unreachable | Add the `INDEX.md` row | One row |
| Phantom | Remove the row, or write the missing document if it should exist | Say which you chose and why |

**Code wins over documentation, always.** A document is a description; the code is the thing. When they
disagree, the document is wrong until proven otherwise — unless the code contradicts an explicit
requirement, in which case the code is the defect and you raise it rather than documenting the bug as
intended behaviour.

## Recording the heal

Every heal leaves two traces:

1. **A row in [`../INDEX.md`](../INDEX.md)** — added, updated, or its status changed.
2. **A line in the current artefact's Discoveries or Corrections section** — the execution report for
   feature work, the fix report for a bug fix. Format:

> **Doc heal** — `docs/api/webhooks.md:22` claimed the retry limit is 3. Code is 5
> (`src/webhooks/retry.ts:14`). Corrected the doc, updated `INDEX.md` status to current. Paused `S3` task 2,
> resumed after.

That second trace is what makes the pattern visible. A project whose docs need healing in the same area three
times has a structural problem there, and only the record reveals it.

## When to defer instead of heal

Healing is bounded. Defer — with a recorded reason — when:

| Condition | Why | What to do |
|-----------|-----|-----------|
| The correct content is not yet known | Writing a guess is worse than the gap (**F5**) | Record it as debt in `INDEX.md` "Known documentation debt" with what is unknown |
| The fix requires a decision that is the user's | Auto-healing does not settle product questions | Raise it, record it as debt, continue |
| The rewrite is larger than the task in progress | Unbounded scope mid-task is **F7** | Fix the specific wrong statement now; log the rewrite as debt |
| The document is generated | Editing output that regeneration overwrites achieves nothing | Fix the generator, or log it as debt if the generator is out of scope |
| Deleting a document would be the right fix | Deletion is irreversible (**F6**) | Mark superseded, propose deletion, ask |

A deferred heal is **still recorded**. The difference between deferring and ignoring is the row in
`INDEX.md`. Silently walking past a wrong document is the failure this rule exists to prevent.

## Bounding it — the three limits

Auto-healing has a real failure mode: it can consume a session. Three limits:

1. **Depth 1.** While healing, you may notice a second broken document. Record it as debt; do not heal it
   now. Healing a chain is how a one-hour task becomes a documentation project.
2. **Proportionality.** The heal is proportionate to what blocked you. If the document is 90% wrong and you
   needed one paragraph, fix the paragraph and log the rest.
3. **Never mid-edit.** Finish the file edit you are in the middle of — a half-written source file plus a
   context switch is how syntax errors get committed. Pause at the next clean boundary, which is usually
   seconds away.

## Interaction with the tracks

| Moment | How auto-healing applies |
|--------|--------------------------|
| Grounding (`A1`/`B1`) | Contradictions found here are healed immediately — this is where they are cheapest, before anything is built on them |
| Design | A doc contradicting the design's premise is healed before the design proceeds. A design built on a wrong premise is wasted |
| Execution | Heal, then continue the task. Record it in that section's execution report |
| Bug fix (`C2`) | A document contradicted by the root cause is a strong signal. Heal it, and consider a `70-do-not-regress.md` row |
| Close-out | Docs are synced as a step anyway, but anything you deferred as debt is either done here or explicitly carried forward with a reason |
| Activation (`S7`) | Debt found during discovery is **recorded, not healed.** Activation is not the moment to rewrite the project's docs |

## What this is not

- Not a licence to rewrite documents you dislike. The trigger is a concrete defect you hit.
- Not a reformatting pass. Style is not a trigger.
- Not permission to delete. Deletion is irreversible and needs approval.
- Not a substitute for the close-out documentation step. Healing fixes what you tripped over; close-out
  syncs what the change made stale.
- Not applicable to the framework's own universal rules. Those are versioned upstream — if
  [`../rules/`](00-agent-contract.md) `00`–`95` is wrong, report it and note the framework version. Editing
  them in place breaks the re-sync guarantee.
