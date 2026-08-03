<!--
TEMPLATE — the universal fallback adapter. Written at activation step S6.

Copy to the target project's root as AGENTS.md — or to whatever path YOUR mechanism uses, per
ide-matrix.md. The filename and any front matter change per tool; the CONTENT CONTRACT below does not.

DELETE THIS HEADER BLOCK. Then replace every <angle-bracket> value from .ai/config.yml and the generated
project rule layer. A surviving placeholder in a live adapter is a rule nobody can follow.

THE ONE HARD RULE: THIS FILE REFERENCES THE RULES. IT DOES NOT RESTATE THEM. Two copies of a rule drift apart
— that is F11, and it is the failure this framework spends the most structure preventing. The eleven
non-negotiables in MAIN.md are the single sanctioned exception, and the short list below is a deliberate,
minimal echo of the same reasoning: short, stable, and expensive to miss.

TARGET LENGTH: ABOUT ONE PAGE. An adapter that grows into a second rule set has recreated the drift problem.
-->

# AGENTS.md — <project name>

Instructions for any AI coding assistant working on this repository.

The full rule set lives in `.ai/`. **This file points at it; it does not duplicate it.** Everything here is a
pointer plus the few things too expensive to leave behind one.

Framework: Generic-GenAI-Setup v<version> (1.1.0 or later — earlier versions lack the preflight). Contract: `.ai/manifest.md`. Everything's location:
`.ai/INDEX.md`.

---

## The project

<One paragraph from `.ai/rules/project/product.md`. What it does, for whom, the primary actors. Never
invented — if the codebase does not establish it, say so.>

<Stack line: language, framework, data store, key libraries, from `.ai/rules/project/tech.md`.>

## Non-negotiables

Short enough to state here, and too important to leave behind a pointer.

1. **The four gates are the definition of done.** Real output, never the claim.
2. **A pointer is never evidence.** An index hit or search match tells you where to look. Verify by reading
   the file. A claim that something does **not** exist needs two independent checks by *different* methods.
3. **Ask before anything irreversible.** Dropping or renaming live data · deleting a live route, endpoint,
   component, or field · changing permissions · sending mail · anything touching money. Removals never ride
   inside feature work — they go to a separately approved cutover step.
4. **Classify work before starting it** — see "How work starts" below.
5. **Two strikes.** If the same approach fails twice, stop patching. State what the failures rule out and
   change approach.
6. **Never reintroduce anything in `.ai/rules/70-do-not-regress.md`.**
7. **Constants and environment come from their declared modules** — `<constants path>` and `<env path>`.
   Never hardcode; never echo a secret's value.

Full contract: `.ai/rules/00-agent-contract.md`. The complete eleven: `MAIN.md`.

## The four gates

```bash
<lint command>        # zero errors AND zero warnings
<static command>      # zero errors
<build command>       # succeeds
<test command>        # full suite, single terminating run
```

<Additional gates and the condition that makes each required.>

All four, every time. Paste the output. `.ai/config.yml` → `gates` is the machine-readable source.

## How work starts

Every non-trivial change goes through a track. **The user states it**; you may propose another with a reason,
but ask first.

| Track | When | Steering | Raw requirement |
|-------|------|----------|-----------------|
| **A** | New feature — a capability that does not exist | `.ai/rules/81-track-a-new-feature.md` | `.ai/templates/raw-requirement-new-feature.md` |
| **B** | Enhancement — changing an existing feature | `.ai/rules/82-track-b-enhancement.md` | `.ai/templates/raw-requirement-enhancement.md` |
| **C** | Bug fix — shipped behaviour is wrong | `.ai/rules/83-track-c-bug-fix.md` | `.ai/templates/raw-requirement-bug-fix.md` |
| **T** | Trivial — copy, constant, one-line guard | none | none |

Track T is void the moment the change touches a type, a schema, a role, a response shape, or routing
configuration — those are Track B.

Round caps are **hard, no escape hatch** — A: 3 gap / 3 design / 2 tasks · B: 2 / 2 / 1. At a cap the stage
closes and open items become recorded residual risks.

Artefacts: `<artefact_root>/<slug>/**`, or `<artefact_root>/hotfixes/<slug>/**` for bug fixes. A template for
every step output: `.ai/templates/README.md`.

## Read these before acting

The always-loaded set. Plain Markdown, readable by any assistant.

| File | Covers |
|------|--------|
| `.ai/rules/00-agent-contract.md` | The operating contract |
| `.ai/rules/01-session-preflight.md` | **The preflight block — no file edit before it.** See below |
| `.ai/rules/05-failure-detection.md` | Failure signals `F1`–`F13` and the first move for each |
| `.ai/rules/50-security.md` | Secrets, validation, authorisation, dependencies, privacy |
| `.ai/rules/60-workflow-and-verification.md` | Grounding rule, gates, irreversible actions, resuming |
| `.ai/rules/62-3x-verification.md` | The three passes before anything is called complete |
| `.ai/rules/65-auto-healing-docs.md` | A wrong document pauses the task: fix it, index it, resume |
| `.ai/rules/70-do-not-regress.md` | Must-not-reintroduce register |
| `.ai/rules/95-retrieval.md` | How to find things without reading directories wide |
| `.ai/rules/project/product.md` · `structure.md` · `tech.md` · `10-code-conventions.md` | This project specifically |

**Load when relevant** — listed so they are discoverable even where automatic loading is unsupported:

| File | Load when |
|------|-----------|
| `.ai/rules/80-work-intake.md` | starting any non-trivial change |
| `.ai/rules/project/15-design-system.md` | touching `<ui globs>` |
| `.ai/rules/project/20-api-standards.md` | touching `<api globs>` |
| `.ai/rules/project/30-data-and-types.md` | touching `<data globs>` |
| `.ai/rules/project/90-docs-map.md` | needing the curated reading order over project docs |
| `.ai/rules/84-failure-playbooks.md` | a first move did not clear a failure |
| `.ai/knowledge/ai_troubleshooting.md` | understanding *why* something keeps going wrong |
| `.ai/templates/task-formats/README.md` | planning a task — pick the discipline format |
| `.ai/retrieval/build-index.md` | the index is empty or stale, or `S7` needs re-running |
| `.ai/setup/install-audit.md` | the framework seems installed but is not being followed |

## Finding things — don't sweep directories

<Tier 0/1: `.ai/INDEX.md` first, then scoped exact search over `<source roots>`. Do not read directories
wide — that is **F4**, and it also buries the constraints you were given.>

<Tier 2: paste the real commands.>

```bash
<index stats command>
<index build command>
<index query command>
```

Then open the returned pointers and **read the real file**. Detail: `.ai/rules/95-retrieval.md`.

## When something goes wrong mid-task

`.ai/rules/05-failure-detection.md` is a signal → code → first-move table for thirteen failure modes. Worth
knowing by heart:

- **F5** about to state something exists without reading it → read it
- **F3** same failure twice → stop patching, change approach
- **F9** about to say done with a follow-up open or a gate unrun → not done
- **F6** about to do something irreversible → stop and ask
- **F11** two documents instruct differently → stop and ask which wins

## Before the first edit of any session

Emit the preflight block. No file edit before it. Full rule:
`.ai/rules/01-session-preflight.md`.

```
PREFLIGHT
Track:     <A | B | C | T> — <slug>, or "unclassified — asking"
Stage:     <step> · resume read: <artefact path, or "new work">
Gates:     <the four commands above>
Retrieval: tier <n> — <query command, or "INDEX.md + scoped search">
Pins:      <files from the feature steering file, or "none yet">
```

Three lines can halt the work: an **unclassified** track (ask, do not edit), **gates** you cannot fill from
`config.yml` (there is no definition of done), and a **retrieval** tier with no index content (the install is
incomplete — run `.ai/setup/install-audit.md`).

This is the only moment the process is checked. Without it every rule below is an obligation nothing asks for.

## Three standing rules

- **Documentation self-heals.** A document found missing, incomplete, or contradicted by the code pauses the
  current task: fix it, update `.ai/INDEX.md`, then resume. `.ai/rules/65-auto-healing-docs.md`.
- **Nothing completes on one look.** Three verification passes — logic, structure, instruction-adherence — and
  a component that fails one is discarded and regenerated, not patched. `.ai/rules/62-3x-verification.md`.
- **Retrieval is index-first.** `.ai/INDEX.md`, then the tier's search. Never a wide directory read. If the
  index is empty the install is incomplete — say so rather than sweeping. `.ai/rules/95-retrieval.md`.

## Where code goes

```
<from .ai/rules/project/structure.md — the placement rules, not the whole tree>
```

<Rendering, typing, or async conventions worth stating in one line each. Everything else is in
`.ai/rules/project/10-code-conventions.md`.>

## Notes on this file

It **references** the rule files rather than copying them, deliberately: two copies of a rule drift apart,
which is **F11**. The non-negotiables above are the one exception — they are short, and missing one is
expensive.

If you are setting up a different assistant, **do not rewrite or delete `.ai/`**. It is the canonical source.
Add your adapter alongside it and record it in `.ai/adapters/adapters-log.md`.
