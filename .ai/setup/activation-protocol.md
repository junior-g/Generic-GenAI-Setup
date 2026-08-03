# Activation Protocol — `super_saiyan_bann_jo`

**Trigger.** The user sends `super_saiyan_bann_jo`, usually with [`../../MAIN.md`](../../MAIN.md)
attached. It works in any assistant, in any IDE, because it is just a phrase in a message — no plugin,
no command registration, nothing to install.

**What it means.** *"You are a new agent on this project. Discover what the project actually is, discover
how you yourself load persistent instructions, then set yourself up so you behave the way this framework
describes."* Same end state, whatever the tool.

**Read first:** [`../manifest.md`](../manifest.md) — the contract listing what must be loaded, in what
mode, and the fourteen behaviours that must be true when you are done.

> **Do not skip to writing files.** Steps `S1` and `S2` exist because guessing the project's conventions
> or your own is failure mode **F5**, and inlining content you should have referenced is **F11**. Both are
> expensive to undo and both look fine at the time.

Nine steps. Not reorderable. Nothing is written before `S4`.

---

## `S0` — Announce and frame

One short message, before any tool call:

- which assistant/IDE you are, and how you *believe* you load persistent project instructions
- that you are going to verify that belief rather than rely on it
- that you will write nothing until `S4`, and will list every file first
- the framework version you are installing from ([`../INDEX.md`](../INDEX.md) header)

Then continue without waiting, unless something is genuinely ambiguous — for example the repository
contains two projects and you cannot tell which is in scope. That is a real question; ask it.

## `S1` — Discover the project 🔴

By evidence. Never by inference from a directory name or a README claim.

Work through [`discovery-checklist.md`](discovery-checklist.md) §1. The minimum you must establish, each
with the file you read to establish it:

| Finding | Established by reading | Decides |
|---------|------------------------|---------|
| Primary language and version | version/manifest file, not the file extensions | `stack_profile` |
| Package/dependency manager | lockfile present | gate command syntax |
| Lint command | the manifest's script block, or the linter's own config file | `gates.lint` |
| Static/type-check command | ditto; note if the language has none | `gates.static`, `substituted` |
| Build command | ditto | `gates.build` |
| Test command and runner | ditto, plus how to force a single non-watch run | `gates.test` |
| Source, test, UI, API and data roots | the actual tree | `paths.*`, FILE-MATCH globs |
| Existing docs, if any | the tree | `paths.docs_root`, Zone 2 of `INDEX.md` |
| Constants and env access pattern | the modules that hold them | non-negotiable 10 |
| CI configuration | the pipeline file | reveals gates the manifest omits |
| Existing agent instructions | root-level agent files | tells you an adapter may already exist |

**Run every gate command you found, before writing them into config.** A command that fails on a clean
checkout is a finding about the project, not about your setup. Record it; do not fix it as part of
activation.

If a gate genuinely does not exist — no linter configured, no type checker for the language — say so
explicitly and set `substituted: true` with what stands in. Do not delete the gate and do not invent a
command that was never run.

## `S2` — Discover your own instruction mechanism 🔴

Establish how *this* assistant loads persistent project rules, by evidence.

**2a.** Check your own documentation or system context. Many assistants state their convention directly.
If you know it with certainty, say *how* you know.

**2b.** Look for what already exists in the repository — see
[`discovery-checklist.md`](discovery-checklist.md) §2 for the commands.

**2c.** Consult [`../adapters/ide-matrix.md`](../adapters/ide-matrix.md). Treat it as a hypothesis, not
an answer; conventions change and a stale table is exactly the trap `S2` exists to avoid. Correct the
matrix if you find it wrong — that is a contribution, not a deviation.

**2d.** Record: the mechanism, the exact paths, which of the four load modes it supports, and how you
established each.

If you cannot determine it: **use `AGENTS.md`** and say plainly that you could not confirm your native
mechanism so you fell back to the open standard. That is a correct outcome, not a failure.

## `S3` — Map the modes, then show the plan

Produce both tables before writing anything.

**Load-mode mapping:**

| Manifest mode | Files | Your mechanism | Fallback applied? |
|---------------|-------|----------------|-------------------|
| ALWAYS | 9 universal + 4 generated | | |
| AUTO | `80-work-intake.md` | | |
| ON-DEMAND | 10 files | | |
| FILE-MATCH | 3 generated, globs from `S1` | | |

**File plan** — every path you will create or modify, one line each on why. Then write them.

The one hard rule: **reference, do not copy.** Your adapter tells the agent *which files to read*; it does
not restate their content. Copying gives two sources that drift — **F11**. Inline only if your mechanism
genuinely cannot follow a pointer, and then record the hashes per
[`../manifest.md`](../manifest.md) §4.

## `S4` — Install the payload and write `config.yml`

1. Copy the framework's `.ai/` into the project root. Copy, do not symlink — the project must remain
   self-contained when the framework directory is not present.
2. Create `.ai/work/` (and `.ai/work/hotfixes/`) as the artefact root.
3. Copy [`../config.example.yml`](../config.example.yml) to `.ai/config.yml` and fill **every** key from
   `S1` evidence.
4. Anything you could not establish goes in the `unresolved` block with what you checked and why it was
   inconclusive. A populated `unresolved` block is an honest install; a guessed value is not.
5. Add the generated index artefact path to the project's ignore file if `retrieval.tier` is `2`.

Do not delete or rewrite an existing instruction set you found in `S2`. If the project already has
`.kiro/steering/**`, `.cursor/rules/**` or similar, leave it and add alongside — removing it is
irreversible and is not yours to decide (**F6**).

## `S5` — Generate the project rule layer

From [`../rules/project/README.md`](../rules/project/README.md). One output per template:

| Template | Output | Mode |
|----------|--------|------|
| `product.template.md` | `product.md` | ALWAYS |
| `structure.template.md` | `structure.md` | ALWAYS |
| `tech.template.md` | `tech.md` | ALWAYS |
| `10-code-conventions.template.md` | `10-code-conventions.md` | ALWAYS |
| `15-design-system.template.md` | `15-design-system.md` | FILE-MATCH (UI globs) |
| `20-api-standards.template.md` | `20-api-standards.md` | FILE-MATCH (API globs) |
| `30-data-and-types.template.md` | `30-data-and-types.md` | FILE-MATCH (data globs) |
| `90-docs-map.template.md` | `90-docs-map.md` | ON-DEMAND |

Generate a file only where the project has that layer. A CLI tool with no UI does not get a design
system — record the omission in `README.md` of the project rules folder so a later session knows it was a
decision, not an oversight.

**Every statement in a generated rule carries a `file:line` pointer** to the code that establishes it.
A convention you inferred from one file is a hypothesis; check three call sites before writing it as a
rule. Where the codebase is genuinely inconsistent, say so and state which pattern is preferred going
forward rather than pretending a convention exists.

Append project-specific rows to `70-do-not-regress.md` for anything the codebase clearly fenced off —
a deprecated helper still present with a comment telling you not to use it, a config key marked
superseded, a pattern the CI lints against.

## `S6` — Write the IDE adapter

Per `S3`'s mapping. Use [`../adapters/AGENTS.template.md`](../adapters/AGENTS.template.md) as the base
even if your mechanism is different — the content requirements are the same, only the filename and
front matter change.

The adapter must contain, at minimum:

1. **What this project is** — one paragraph, from `product.md`. Never invented.
2. **Read these before acting** — the ALWAYS list, as paths.
3. **How work starts** — the A/B/C/T table, and that work-intake classifies it.
4. **The four gates** — the concrete commands from `config.yml`. All four.
5. **The grounding rule** — a pointer is never evidence; negatives need two checks by different methods.
6. **Hard stops** — irreversible actions ask first; removals go to cutover.
7. **Retrieval** — the project's tier and its commands.
8. **Failure detection** — the `F1`–`F13` vocabulary exists; where the table and the playbooks are.
9. **ON-DEMAND and FILE-MATCH files** — listed with purpose and glob, so they are discoverable even if
   your IDE cannot auto-trigger them.
10. **The auto-healing rule and the 3× verification rule** — one line each, pointing at the rule files.

Keep it to roughly one page. An adapter that restates the whole rule set has recreated the drift problem.

If your mechanism supports front matter or globs, use it — it is closer to the original behaviour and
reproduces FILE-MATCH properly instead of degrading it.

## `S7` — Build the index 🔴

**Every tier has a deliverable. There is no tier at which `S7` produces nothing.**

This is the step most often skipped, because choosing tier 0 feels like a decision to build nothing. It is
not: tier 0 *is* a hand-built index, and an install that chose tier 0 and wrote no index has **skipped** `S7`
rather than completed it. That mistake gets reported as "the setup missed the indexing logic", and it is the
single most common activation failure.

**1. Choose the tier** per [`../retrieval/index-spec.md`](../retrieval/index-spec.md) §2 and record it in
`config.yml` with a written rationale. Do not default to tier 2 because it is the most sophisticated; a small
project is better served by tier 0, and the decision is reversible.

**2. Build it** per [`../retrieval/build-index.md`](../retrieval/build-index.md). Deliverables:

| Tier | Must exist when `S7` closes |
|------|----------------------------|
| **0** | `.ai/INDEX.md` Zone 2 fully populated — documents with a read status, entry points, layers, the six most-asked questions, empty work tables, recorded debt |
| **1** | All of tier 0, plus the IDE's semantic search **verified** to index this repository |
| **2** | All of tier 0, plus a working engine: build, stats, query, route, self-test in `gates.extra`, artefacts gitignored, all four commands in `config.yml` |

**3. Delete the pristine markers.** The framework copy ships Zone 2 with placeholder rows and a
`Zone 2 status: not generated` line. A surviving placeholder is how install-audit row A5.2 fails, and it is the
difference between an index and an index-shaped file.

**4. Report the tier and its consequence** — two sentences to the user: the tier, why, and **what it does not
give them**. Without that sentence the work is invisible, and invisible work gets reported as missing.
[`../retrieval/build-index.md`](../retrieval/build-index.md) §6 has the wording.

Documentation debt found here is *recorded*, not fixed. Activation is not the moment to rewrite the project's
docs (**F7**); the auto-healing rule
([`../rules/65-auto-healing-docs.md`](../rules/65-auto-healing-docs.md)) picks each item up the first time real
work touches it.

## `S8` — Prove the behaviour, then log 🔴

Activation is not complete when the files exist. It is complete when the behaviour is demonstrably true.

Work through all twelve checks in [`../manifest.md`](../manifest.md) §3. For each, state the probe, the
response, and pass/fail:

| # | Behaviour | Probe used | Result |
|---|-----------|-----------|--------|
| 1 | Work classified before starting | | ☐ |
| 2 | Pointer is not evidence | | ☐ |
| 3 | Four gates define done | | ☐ |
| 4 | Round caps are hard | | ☐ |
| 5 | Irreversible actions stop and ask | | ☐ |
| 6 | Failure signals named by code | | ☐ |
| 7 | Retrieval before directory sweeps | | ☐ |
| 8 | Every step has a template | | ☐ |
| 9 | Artefacts have a declared home | | ☐ |
| 10 | Do-not-regress register binds | | ☐ |
| 11 | Documentation self-heals | | ☐ |
| 12 | Nothing completes on one look | | ☐ |

A box you cannot tick is a gap in the adapter. Fix the adapter, do not lower the bar. If a check remains
unticked after a fix attempt, record it as a known gap rather than claiming 14/14.

Then continue to `S9`. Logging happens there, once both scores are known.

## `S9` — Install audit, then log 🔴

`S8` proved the agent *behaves* correctly. `S9` proves the *artefacts exist and are filled*. They fail
differently, which is why they are separate steps: an install can score full marks on behaviour while having an
empty index and an unfilled `config.yml`, because behaviour probes ask questions and never open a file.

Work through [`install-audit.md`](install-audit.md) — seven blocks, 69 rows. Report a score per block.

| Block | Checks | Blocking rows |
|-------|--------|---------------|
| A1 payload | 6 | — |
| A2 `config.yml` 🔴 | 11 | all of it — no gate commands means no definition of done |
| A3 project rules | 13 | 3.11 a surviving template header means the file was copied, not generated |
| A4 adapter | 11 | 4.8 preflight not referenced → the process will not be followed |
| A5 indexing 🔴 | 8 (+6 tier 2) | 5.2 Zone 2 empty → no index exists |
| A6 process reachable | 6 | 6.2 work-intake unreachable → work is never classified |
| A7 behaviour | 14 | — |

**Fix every blocking gap before reporting the install complete.** A non-blocking ❌ is recorded and left to the
user's judgement.

Then append an entry to [`../adapters/adapters-log.md`](../adapters/adapters-log.md) using the template at the
bottom of that file, and tell the user in a few sentences: the mechanism you used, what you created, the two
scores, the retrieval tier **and what it does not give them**, what degraded and how you compensated, and
anything you could not confirm.

---

## Re-activation and re-sync

`super_saiyan_bann_jo` in an already-installed project is a **re-sync**, not a fresh install:

1. Compare `.ai/config.yml`'s `framework.version` against the framework home's version.
2. Refresh the universal rules (`rules/00`–`95`), the templates, the manifest and the retrieval spec —
   these are copied verbatim and safe to overwrite.
3. **Never overwrite** `rules/project/**`, `config.yml`, `INDEX.md` Zone 2, or `work/**`. Those are the
   project's own. Report differences and let the user decide.
4. Re-run `S8`. A framework upgrade can change what the behaviour checks expect.
5. Log the re-sync in `adapters-log.md` with both versions.

## Anti-patterns

Each of these produces a setup that looks finished and is not.

| Don't | Why | Instead |
|-------|-----|---------|
| Assume your own instruction convention | **F5** — the guess is often a version out of date | `S2` |
| Assume the project's gate commands from the language | **F5** — projects wrap and rename constantly | `S1`, and run them |
| Write gate commands you never executed | **F5**, and the first real gate run fails | Run each one at `S1` |
| Copy rule content into the adapter | **F11** — two sources, guaranteed drift | Reference by path |
| Write the adapter and stop | **F9** — an untested setup is not a setup | `S7`, `S8` |
| Default to tier 2 retrieval to look thorough | **F7**, **F12** — cost with no return on a small corpus | `S7`, honestly |
| Generate a design-system rule for a project with no UI | **F5** — a rule about nothing dilutes the ones that matter | Omit and record why |
| Fix the project's documentation during activation | **F7** — unbounded scope before any work has started | Record as debt at `S7` |
| Delete an instruction set you don't use | **F6** — irreversible, and not your call | Leave it, add alongside |
| Claim 14/14 without running the probes | **F8**, **F9** — the one failure the whole protocol exists to prevent | Run them, report the real number |
