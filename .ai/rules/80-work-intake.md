# Work Intake

**Load mode: AUTO** — loads when work is being started. Its job is to classify, then hand off.

Every non-trivial change enters through one of four tracks. Classify first, then pull in that track's file.
Do not improvise a process.

| Track | When | Load |
|-------|------|------|
| **A** | New feature — a capability that does not exist. New surfaces, new data, possibly a new actor | [`81-track-a-new-feature.md`](81-track-a-new-feature.md) |
| **B** | Enhancement — changes an existing feature. New field, state, or screen on surfaces that already exist; live data may exist | [`82-track-b-enhancement.md`](82-track-b-enhancement.md) |
| **C** | Bug fix — shipped behaviour is wrong and reproducible | [`83-track-c-bug-fix.md`](83-track-c-bug-fix.md) |
| **T** | Trivial — copy, constant, token, one-line guard. No new state, no new surface, no data change | nothing; see below |

**The user states the track** in the raw requirement. You may propose a different one with a reason, but you
**ask before switching**. Silently re-classifying is how a bug fix acquires a design stage, and how an
enhancement skips its regression sweep.

---

## Triage — answer these before classifying

Cheapest possible signal, no file reading required. Any "yes" rules out Track T. Several "yes" plus a new
actor means Track A.

- Does it add a route, screen, endpoint, command, or consumer that does not exist?
- Does it add or change a table, collection, schema, or a field on one that has live records?
- Does it change an existing response shape, event payload, or public contract?
- Does it touch authentication, authorisation, roles, or routing configuration?
- Does it introduce a new actor, or give an existing actor a capability they did not have?
- Is live data already present on the surfaces being touched?
- **What must not break?**

That last one is not a classification question — it is the question whose answer becomes
[`../templates/do-not-break.md`](../templates/do-not-break.md). Ask it every time, on every track.

## Deciding between adjacent tracks

The boundaries are where classification goes wrong. These are the discriminators:

| Situation | Track | Why |
|-----------|-------|-----|
| The feature exists but is badly designed and needs rethinking | **B** | Existing contracts constrain it, which is Track B's whole subject |
| The feature exists and is *wrong* against its own spec | **C** | Shipped behaviour deviating from intent is a defect |
| Investigation shows the behaviour is as designed and the *design* is wrong | **B**, with the user's agreement | Not a bug. Say so and switch; never redesign inside a hotfix |
| A new capability, but it reuses existing infrastructure heavily | **A** | The capability is new. Reuse is a design decision, not a classification |
| Adding a field to a table with live rows | **B** | Migration and back-compat are in scope by default |
| Adding a field to a table with no live rows | **B** | Still a schema change; Track T is void the moment schema is touched |
| Three small unrelated changes | classify **each** | A batch is not a track. The largest one does not absorb the others |
| A defect that will not reproduce | **STOP** | **F5 F8** — never fix speculatively |
| An urgent production defect | **C**, at full rigour | Urgency does not remove the root-cause gate. It selects the track, not the standard |

## Track T in full

Track T is the entire process for trivial changes:

1. State the change in one line.
2. Make it.
3. Run all four gates.
4. Report in one line, with the gate result.

No artefacts, no templates, no rounds. Passes P1 and P3 of the 3× verification rule apply; P2 has nothing
to check.

**Track T is void the moment the change touches** a type or schema · a table or collection · a role or
permission · an API response shape · routing configuration · anything with live data · anything on
[`70-do-not-regress.md`](70-do-not-regress.md). Those are Track B, however small the diff looks.

The trap is a "one-line" change to a shared type. The diff is one line; the blast radius is every caller.

---

## Rules that bind every track

1. **Grounding.** A search or index result is a pointer, never evidence. Any claim that something exists — or
   does not — is verified by reading the file. Negatives need two independent checks by different methods.
2. **Round caps are hard.** No escape hatch. At a cap the stage closes; unresolved items become carried
   questions or recorded residual risks, never another round.
3. **`STOP` means wait for the user.** It is never a self-resolving loop.
4. **All four gates, every time.** `config.yml` → `gates`. Real output, not a claim.
5. **Irreversible actions need approval.** Removals never ride inside a feature section — they go to a
   separately approved cutover step. Add the new path, verify it, then remove the old one.
6. **Two strikes.** Two failures of the same approach ends the approach, not just the attempt.
7. **Resume from artefacts, not memory.** The status table in `tasks/README.md` and the latest execution
   report, before anything else.
8. **Per-feature steering.** Tracks A and B create `project/8x-<slug>.md` from
   [`../templates/feature-steering.md`](../templates/feature-steering.md), pull it in at every session start,
   and **delete it at close-out**.
9. **3× verification** before anything is called complete — [`62-3x-verification.md`](62-3x-verification.md).
10. **Documentation self-heals** the moment it is found wrong —
    [`65-auto-healing-docs.md`](65-auto-healing-docs.md).

## Where things live

Read from `config.yml` → `paths`:

| Artefact | Location |
|----------|----------|
| Feature / enhancement | `<artefact_root>/<slug>/**` |
| Bug fix | `<artefact_root>/hotfixes/<slug>/**` |
| Per-feature steering | `<rules_root>/8x-<slug>.md` — deleted at close-out |
| Everything's map | [`../INDEX.md`](../INDEX.md), Zone 2 |

`<slug>` is lowercase, hyphenated, and stable for the life of the work. It appears in the folder name, the
steering filename, and the `INDEX.md` row, so renaming it later breaks three things at once.

Templates for every step output: [`../templates/README.md`](../templates/README.md).

## Choosing a task format

Within a track, each execution section has a discipline — UI, backend, database, infrastructure, data, or
integration. The task plan for that section uses the matching format from
[`../templates/task-formats/README.md`](../templates/task-formats/README.md), which adds the checks specific
to that discipline on top of the common task blocks.

A section that spans two disciplines uses both formats' discipline-specific blocks. That is usually a signal
the section should be split.

## The output of intake

The track goes into the preflight block
([`01-session-preflight.md`](01-session-preflight.md)), which is emitted before the first file edit. Intake
itself produces one short statement, before any track file is loaded:

> **Track B.** Adding an `archived` state to customer records. Existing surfaces: the list screen and the
> detail screen. Live data exists, so migration and back-compat are in scope. New actor: none. Must not
> break: the existing list filter contract and the CSV export column order. Caps 2 / 2 / 1. Budget: one
> stage this session.

If you cannot write that statement, you do not yet know what you are doing, and reading code will not tell
you — ask.
