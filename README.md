# Generic-GenAI-Setup

A portable, language-agnostic, IDE-agnostic framework for making an AI coding agent behave like a
disciplined engineer on any codebase.

It is not a prompt. It is a rule set, a process with hard caps, an artefact template for every step, a
failure taxonomy with remediation playbooks, and a setup protocol that adapts all of it to whatever
project and IDE it lands in.

| | |
|---|---|
| Activate with | **`super_saiyan_bann_jo`** |
| Anchor file | [`MAIN.md`](MAIN.md) — attach this to the chat |
| Payload | [`.ai/`](.ai/INDEX.md) — copied into the target project at activation |
| Dependencies | none. Plain Markdown and one YAML config. No runtime, no package, no API key |

---

## Why this exists

An AI agent on a large codebase fails in predictable ways: it claims a file contains something it never
opened, it drifts from the original objective halfway through a long session, it retries the same broken
command four times, it declares a task done with three follow-ups open, it deletes something "impact-free"
that a live page depended on.

None of those are fixed by a better prompt. They are fixed by structure: an externalised state machine so
completion is not held in context, a grounding rule so a search hit is never mistaken for a fact, hard
round caps so planning cannot expand to fill the budget, and a classification step so a bug fix never
enters the machinery built for new features.

This framework is that structure, extracted from a codebase where it was built incrementally in response
to real defects, and generalised so it drops into anything.

## Quickstart

**In a new project, with an AI assistant that has file access:**

```
super_saiyan_bann_jo
```

…and attach `MAIN.md`, or point the assistant at `~/Documents/Generic-GenAI-Setup/`.

The assistant then runs [`.ai/setup/activation-protocol.md`](.ai/setup/activation-protocol.md): it
inspects the project, discovers how your IDE loads persistent instructions, copies `.ai/` in, generates
the project-specific rule layer, writes an IDE adapter, and proves twelve behaviour checks before
reporting done.

**Then, to start work:**

```
Track B. Add pagination to the customer list endpoint.
```

The assistant pulls in the Track B steering, fills the enhancement raw-requirement template, grounds
itself with file-verified pointers, builds a `do-not-break.md` register, and proceeds through the capped
stages.

## What activation produces in your project

```
<your project>/
├── .ai/                          the framework payload, plus:
│   ├── config.yml                your gate commands, paths, stack profile, IDE adapter
│   ├── INDEX.md                  the central index, regenerated for your project
│   ├── rules/project/            product.md · structure.md · tech.md · conventions · design · api · data
│   └── work/                     artefacts: <slug>/** per feature, hotfixes/<slug>/** per bug
└── <IDE adapter>                 e.g. AGENTS.md, .cursor/rules/*.mdc, CLAUDE.md, .kiro/steering/*
```

Nothing else in your repository is touched. Source code, build config and CI are left alone unless the
work you subsequently ask for changes them.

## The shape of the process

```
        ┌──────────────┐
        │ work intake  │  classify: A / B / C / T
        └──────┬───────┘
               │
   ┌───────────┼───────────┬──────────────┐
   ▼           ▼           ▼              ▼
Track A     Track B     Track C        Track T
new         change to   reproducible   copy, token,
capability  a shipped   defect         constant
            feature
   │           │           │              │
   │           │           │              └─ state → change → gates → one-line report
   │           │           │
   │           │           └─ reproduce → written root cause → blast radius → minimal
   │           │              fix → fails-before/passes-after test → gates → harden
   │           │
   │           └─ + do-not-break register + regression sweep, caps 2/2/1
   │
   └─ frame → ground → pin → gaps (≤3) → requirement → design → design review (≤3)
      → tasks → tasks review (≤2) → execute per section → cutover → close out
```

Every arrow above has an artefact template. Every stage has a hard cap. Every claim inside those
artefacts carries a `file:line` pointer marked read-and-verified.

## Core guarantees

| Guarantee | Mechanism |
|-----------|-----------|
| Work is classified before it starts | [`80-work-intake.md`](.ai/rules/80-work-intake.md) |
| A pointer is never treated as evidence | grounding rule in [`60-workflow-and-verification.md`](.ai/rules/60-workflow-and-verification.md) |
| Four gates define done, with real output | [`60-workflow-and-verification.md`](.ai/rules/60-workflow-and-verification.md) + `config.yml` |
| Planning cannot expand indefinitely | hard round caps in each track file |
| Irreversible actions stop and ask | [`00-agent-contract.md`](.ai/rules/00-agent-contract.md) + cutover step |
| Failures are named, not improvised around | [`05-failure-detection.md`](.ai/rules/05-failure-detection.md) → [`84-failure-playbooks.md`](.ai/rules/84-failure-playbooks.md) |
| Documentation self-heals during work | [`65-auto-healing-docs.md`](.ai/rules/65-auto-healing-docs.md) |
| Nothing ships on one look | [`62-3x-verification.md`](.ai/rules/62-3x-verification.md) |
| Setup is provably installed | 12 behaviour checks in [`.ai/manifest.md`](.ai/manifest.md) |

## Language and IDE independence

**Language.** The framework never names a language. The four gates are *roles*, not commands: LINT,
STATIC/TYPE, BUILD, TEST. [`.ai/setup/stack-profiles.md`](.ai/setup/stack-profiles.md) maps those roles
onto concrete commands for JavaScript/TypeScript, Python, Java/Kotlin, Go, Rust, C#/.NET, PHP, Ruby,
Swift, C/C++ and Elixir, and tells you how to add a profile for anything missing. The chosen mapping is
recorded once, in `.ai/config.yml`.

**IDE.** Rule loading is abstracted into four modes — ALWAYS, AUTO, ON-DEMAND, FILE-MATCH — each with a
defined fallback for IDEs that cannot do it.
[`.ai/adapters/ide-matrix.md`](.ai/adapters/ide-matrix.md) covers the known conventions;
[`.ai/adapters/AGENTS.template.md`](.ai/adapters/AGENTS.template.md) is the universal fallback that most
assistants read natively. Adapters reference the rule files; they never copy them.

## Repository map

| Path | Contents |
|------|----------|
| [`MAIN.md`](MAIN.md) | activation anchor, the eleven non-negotiables, the `S0`–`S8` summary |
| [`.ai/`](.ai/INDEX.md) | the portable payload — see [`.ai/INDEX.md`](.ai/INDEX.md) |
| [`docs/rationale.md`](docs/rationale.md) | why each rule exists, with the failure that produced it |
| [`docs/CHANGELOG.md`](docs/CHANGELOG.md) | framework version history |

## Maintaining the framework itself

The framework is versioned in [`docs/CHANGELOG.md`](docs/CHANGELOG.md), and every installation records
the version it was installed from in `.ai/config.yml`. Improving a rule means editing it here, bumping
the version, and noting in the changelog whether existing installations need to re-sync.

Universal rules (`.ai/rules/00`–`95`) are copied verbatim into projects and **never edited in place** —
that is what makes a re-sync safe. Project-specific content goes in `.ai/rules/project/`, which the
framework never overwrites after generation.
# Generic-GenAI-Setup
