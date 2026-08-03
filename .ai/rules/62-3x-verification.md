# The 3× Verification Rule

**Load mode: ALWAYS.** It applies at the moment you are about to call something complete, which can happen
at any turn.

Nothing is marked complete on one look. Every generated file, section, artefact, or code change goes through
**three distinct passes**, each asking a different question. A component that fails a pass is **discarded and
regenerated**, not patched over.

The three passes are not "read it three times". Re-reading with the same question produces the same answer —
that is why the passes ask different questions in a deliberate order.

---

## Why three, and why these three

| Pass | Question | Catches | Would pass 1 catch it? |
|------|----------|---------|------------------------|
| **P1 — Logic** | Is it *correct*? | wrong behaviour, bad edge cases, security holes, false claims | — |
| **P2 — Structure** | Does it *hold together*? | broken links, orphans, contradictions, index drift, missing sections | No — a logically perfect file can still point at a file that does not exist |
| **P3 — Instruction** | Is it *what was asked for*? | scope creep, a dropped requirement, a violated convention, a placeholder left in | No — correct, coherent work can still be the wrong work |

Order matters. Structure is cheap to check but meaningless on broken logic. Instruction-adherence is the
last gate because it is the one that decides whether the work should exist at all, and finding that out
after the other two is cheaper than the reverse.

---

## Pass 1 — Logic and correctness

Read the artefact as an adversary who wants it to be wrong.

| # | Check | Fails when |
|---|-------|-----------|
| 1.1 | Every factual claim is verified against the real file, cited `path:line` | a claim rests on a search hit rather than a read (**F5**) |
| 1.2 | Every negative claim has two independent checks by different methods | one scoped search stands in for a proof of absence (**F5**) |
| 1.3 | Error and edge behaviour is specified, not implied | empty, null, zero, one, maximum, duplicate, concurrent, and unauthorised cases are unaddressed |
| 1.4 | The security checklist in [`50-security.md`](50-security.md) passes for every boundary touched | a new input is unvalidated, or a new surface is unauthorised and unstated |
| 1.5 | Reversibility is classified for every action | an irreversible action is sitting inside ordinary work (**F6**) |
| 1.6 | Numbers, versions and names are real | any figure that was not measured or read (**F5**) |
| 1.7 | For code: the four gates pass, with real output | a gate was not run, or was made to pass by weakening it |
| 1.8 | For a fix: the regression test failed before and passes after, both observed | the test has never been seen red |

**Discard trigger:** two or more failures in P1, or any single failure at 1.1, 1.2, 1.4, or 1.5. Those four
mean the reasoning underneath the artefact is wrong, and editing the surface leaves it wrong.

## Pass 2 — Structural integrity

Read only the skeleton. Ignore whether the content is right; ask whether the thing is intact.

| # | Check | Fails when |
|---|-------|-----------|
| 2.1 | Every internal link resolves to a real path | a reference points at a file that does not exist or moved |
| 2.2 | Every file is reachable from [`../INDEX.md`](../INDEX.md) | an orphan exists that nothing points at |
| 2.3 | `INDEX.md` has no rows for files that are gone | the index is ahead of reality |
| 2.4 | No section of the template was deleted | a section is absent rather than marked `None` — an empty section and a forgotten one look identical |
| 2.5 | Cross-artefact facts agree | requirement, design and tasks state different versions of the same thing (**F11**) |
| 2.6 | Numbering is unbroken and unique | `R4` appears twice, or the list jumps `S2` → `S4` |
| 2.7 | Every requirement traces forward to a design section and a task; every task traces back | an orphan in either direction |
| 2.8 | No placeholder survives | `TODO`, `FIXME`, `lorem`, `<...>`, `tbd`, a stub returning a fake value |
| 2.9 | Tables are well-formed and headers match their columns | a row has the wrong arity, or a header lies about its column |
| 2.10 | Code blocks declare a language and contain runnable content | a snippet that cannot be executed or copied usefully |

**Discard trigger:** failures at 2.5 or 2.7. A contradiction between artefacts or a broken traceability
chain means the set is not coherent, and fixing one file leaves the set incoherent.

Mechanical helpers — run them, do not eyeball them:

```bash
# 2.1 broken internal links in a markdown tree
grep -rhoE '\]\(([^)#][^)]*)\)' --include='*.md' . | sed -E 's/^\]\(|\)$//g' | sort -u | \
  while read -r p; do [ -e "$p" ] || echo "MISSING: $p"; done

# 2.8 surviving placeholders
grep -rniE 'TODO|FIXME|lorem ipsum|\btbd\b|XXX' --include='*.md' --include='*.yml' .
```

Adjust the first command's base directory to the artefact root; relative links resolve against the file
that contains them, so run it from there or resolve accordingly.

## Pass 3 — Adherence to instruction

Put the original requirement side by side with the artefact.

| # | Check | Fails when |
|---|-------|-----------|
| 3.1 | Every stated requirement is addressed | one was quietly dropped |
| 3.2 | Nothing outside the requirement was built | scope creep — anything not traceable to a requirement number |
| 3.3 | The explicit out-of-scope list is respected | something the user excluded is present anyway |
| 3.4 | The project's conventions are followed | a convention from `project/10-code-conventions.md` is violated |
| 3.5 | Nothing on the do-not-regress register was reintroduced | a fenced pattern is back — see [`70-do-not-regress.md`](70-do-not-regress.md) |
| 3.6 | The definition of done in the requirement is met, item by item | an item is plausibly met rather than observably met |
| 3.7 | The track's own rules were followed | a stage exceeded its cap, or a `STOP` was self-resolved |
| 3.8 | Removals were deferred to cutover, not executed inline | a removal rode along inside feature work (**F6**) |
| 3.9 | The response format matches what was asked for | a document was produced where a change was wanted, or the reverse |

**Discard trigger:** failures at 3.2, 3.3, 3.5, or 3.8. Each means the work is outside its mandate, and no
amount of polish makes out-of-scope work in-scope.

---

## Discard and regenerate

When a discard trigger fires:

1. **Stop.** Do not edit the artefact.
2. **Write down the specific failure** — which check, and what the evidence was.
3. **Identify what the failure implies about your approach**, not just about the output. A false claim in
   P1.1 means the grounding step was too shallow, and regenerating from the same shallow grounding
   reproduces it.
4. **Regenerate from the corrected input.** Fix the source first: re-ground, re-read the requirement, or
   re-derive the root cause.
5. **Re-run all three passes** on the regenerated artefact. A regeneration that only gets pass 1 re-run has
   only been checked for the failure you already knew about.

**Second discard on the same artefact = F3.** Two regenerations failing the same way means your model of
the problem is wrong, not your execution. Stop, write down what the two attempts rule out, and change
approach at the level of the theory. Do not attempt a third variation of the same idea.

## What passes look like when reported

Not a claim — an outcome per pass, with what was checked:

> **3× verification, `design/design-02-notifications.md`**
> **P1 Logic** — pass. 7 claims, each `path:line`-cited and read. One negative ("no existing scheduler")
> checked twice: unscoped glob on `*schedul*`, and read the job registry at `src/jobs/registry.ts:1-40`.
> **P2 Structure** — 1 finding, fixed: link to `design-01-delivery.md` was `design-1-delivery.md`. Traceability
> `R3`,`R4`,`R7` → `S2`,`S3` complete both directions. No placeholders.
> **P3 Instruction** — pass. `R1`–`R8` all addressed. Retry-backoff table removed: not traceable to a
> requirement (P3.2), raised as a follow-up instead.

Compare with what it must never look like: *"Verified three times, all good."* That statement contains no
information and cannot be checked, which makes it **F8**.

## When the rule is proportionate

Three passes on a one-line constant change is theatre. Scale honestly:

| Work | Passes |
|------|--------|
| Track T trivial change | P1 and P3 only, one line each. P2 has nothing to check |
| Any artefact document | all three, fully |
| Any code section | all three, with P1.7 gate output pasted |
| A bug fix | all three, with P1.8 fails-before/passes-after evidence |
| The final review at the end of a track | all three, over the whole artefact set rather than one file |

Skipping a pass because the work is small is fine when it is genuinely true that the pass has nothing to
check. Skipping it because you are confident is **F8**.
