# Workflow and Verification

**Load mode: ALWAYS.** Contains the grounding rule and the gates — the two things most often skipped, and
the two whose absence is most expensive.

---

## Starting work

Any non-trivial change enters through a track. Classify first, then load that track's file:
[`80-work-intake.md`](80-work-intake.md). Do not improvise a process.

| Track | When | File |
|-------|------|------|
| **A** | New feature — a capability that does not exist | [`81-track-a-new-feature.md`](81-track-a-new-feature.md) |
| **B** | Enhancement — changes an existing feature | [`82-track-b-enhancement.md`](82-track-b-enhancement.md) |
| **C** | Bug fix — shipped behaviour is wrong and reproducible | [`83-track-c-bug-fix.md`](83-track-c-bug-fix.md) |
| **T** | Trivial — copy, constant, one-line guard | none; state it, do it, gate it, report in one line |

Artefacts live under `config.yml` → `paths.artefact_root`: `<artefact_root>/<slug>/**` for features and
enhancements, `<artefact_root>/hotfixes/<slug>/**` for bug fixes. Templates for every step output:
[`../templates/README.md`](../templates/README.md).

## Grounding — a pointer is not evidence

An index hit, a search match, a glob result tells you **where to look**. It never establishes a fact.

- Any claim that a file, symbol, route, field, or behaviour **exists** is verified by reading the file, and
  cited as `path:line`.
- Any claim that something **does not exist** — or that removing it is safe — needs **two independent checks
  by different methods**. A negative from a scoped search is not a negative: a search pointed at the wrong
  place returns exactly the same empty result as a search for something absent.
- Acceptable second methods: an unscoped filename glob · reading the module, route table or barrel that
  would consume it · reading the write sites of the data it touches · running the relevant tests · checking
  the dependency manifest.

This rule exists because it has been violated expensively. Two "it isn't there" / "removing it is
impact-free" claims were both wrong, both came from scoped searches, and both survived two review stages.

## The four gates — the definition of done

All four. Every time. Commands are project-specific and live in `config.yml`:

```
gates.lint     → zero errors AND zero warnings
gates.static   → zero type / static-analysis errors
gates.build    → the build succeeds
gates.test     → the full suite passes, in a single terminating run
```

Plus every entry in `gates.extra` whose `when` condition the change satisfies.

**Report the real output.** "Gates pass" without the result is not verification. The execution-report and
fix-report templates both require the evidence, not the claim.

If a gate fails for a reason your change did not cause, say so, show it, and do not silently absorb it. A
pre-existing failure is a finding about the project.

**Never make a gate pass by weakening it.** Widening a type, adding a suppression comment, deleting an
assertion, or excluding a file converts a caught failure into an uncaught one. If a gate is genuinely wrong,
that is a separate, stated change.

## Testing

- New logic gets unit tests. Extract pure logic so it is testable without a database, a network, or a UI —
  that extraction is usually the cheapest quality improvement available.
- New boundaries (endpoints, consumers, jobs) get integration tests.
- Bug fixes get a regression test that **fails before the fix and passes after**, both confirmed by running
  it in each state. A test written after the fix that has never failed proves nothing about the defect.
- If the project has no test framework, set one up using the standard choice for its ecosystem before
  claiming a change is verified. If you cannot, say why explicitly.
- Some things genuinely cannot be automated — real device input, actual mail delivery, generated binary
  output, live payment flows. Say which, say why, and add a manual check instead. Do not pretend coverage.

## Verification is three passes

Running the gates is one input to completion, not completion itself. Before declaring anything complete,
run the three passes in [`62-3x-verification.md`](62-3x-verification.md): logic and correctness, structural
integrity, adherence to instruction. A component that fails a pass is discarded and regenerated, not
patched over.

## Documentation self-heals

A document found missing, incomplete, or contradicted by the code pauses the current task. Fix it, update
[`../INDEX.md`](../INDEX.md), then resume. [`65-auto-healing-docs.md`](65-auto-healing-docs.md).

## Irreversible actions

Ask before: dropping or renaming live data · deleting a live route, endpoint, component, field, or
permission · changing roles or authentication · sending mail or notifications · anything touching money or a
billable third party · rewriting shared git history.

Removals do not ride inside feature work — they go to a separately approved cutover step. **Add the new
path, verify it, then remove the old one. Never the reverse.**

Before retrying anything that already had an external effect, confirm it is idempotent.

## Version control

- Commit only when the user asks. If it is unclear, ask.
- Stage named files rather than everything, so unrelated work does not ride along.
- Flag anything that looks like a secrets file before it is committed.
- Prefer new commits over amending. Never amend after a hook failure — fix, re-stage, new commit.
- Push to a branch, never straight to the default branch, unless explicitly told to.
- Destructive git operations (force-push, hard reset, history rewrite, branch deletion) need explicit
  permission every time.
- Leave hooks and git configuration alone.

## Resuming

On "continue", the resume point is the **artefacts**, not memory and not the conversation:

1. The status table in `<artefact_root>/<slug>/tasks/README.md`.
2. The latest `execution-report-S<n>.md`.
3. Re-pin the feature steering file `project/8x-<slug>.md`.

Do the same after a context compaction. Resuming from memory is **F9**, and it reliably produces work that
duplicates or contradicts what was already done.

## Session budget

Every raw requirement declares a session budget. Check against it at each stage boundary. Running well past
it without saying so is **F12** — report progress and remaining budget and propose phasing rather than
continuing silently.

## Finding context

Retrieval is targeted, never a directory sweep. [`95-retrieval.md`](95-retrieval.md). Curated reading order
over the project's own docs: `project/90-docs-map.md`. Everything's location: [`../INDEX.md`](../INDEX.md).

## When stuck

If an approach fails twice, stop patching. State what the two failures rule out, diagnose the cause, and
change approach — that is **F3** in [`05-failure-detection.md`](05-failure-detection.md), which lists the
signals for the other twelve failure modes and the first move for each. When a first move does not clear
it, or two signals fire together and you need to tell them apart, load
[`84-failure-playbooks.md`](84-failure-playbooks.md).
