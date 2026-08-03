# Manifest — the load-mode contract

The vendor-neutral definition of **what must be in the agent's context, and when**. It exists so the
framework can be reproduced in any IDE without anyone reverse-engineering it, and so a port can be
*proved* rather than assumed.

Rule content lives in [`rules/`](INDEX.md#universal-rules--copied-verbatim-never-edited-per-project) as
plain Markdown with no IDE-specific syntax. Any assistant can read those files directly. What differs
between IDEs is only *when* each file is loaded — which is exactly what this manifest specifies.

Setup runbook: [`setup/activation-protocol.md`](setup/activation-protocol.md), triggered by
`super_saiyan_bann_jo`.

---

## 1. The four load modes

| Mode | Meaning | Fallback if the IDE cannot do it |
|------|---------|----------------------------------|
| **ALWAYS** | In context for every request, no exceptions | None needed — every assistant supports some form of this |
| **AUTO** | Loaded when the request matches a description | Put a three-line summary plus the file path in the ALWAYS adapter, so the agent knows to open it |
| **ON-DEMAND** | Loaded when named by the user or the agent | List the path and a one-line purpose in the ALWAYS adapter so it is discoverable |
| **FILE-MATCH** | Loaded when a matching file is opened or edited | Downgrade to ON-DEMAND and list the glob beside it, so the agent can self-trigger |

**The ALWAYS set is the only thing that must be reproduced perfectly.** Everything else degrades safely
to "named and described", because an agent that knows a file exists and what it covers will open it when
relevant. An agent that has never heard of the file will not.

## 2. The rule set

### ALWAYS — eight universal files plus four generated ones

| File | Why it must always be loaded |
|------|------------------------------|
| [`rules/00-agent-contract.md`](rules/00-agent-contract.md) | The operating contract. Nothing else is safe without it |
| [`rules/05-failure-detection.md`](rules/05-failure-detection.md) | Failure signals fire mid-task; a table you have to go and fetch is a table you do not consult |
| [`rules/50-security.md`](rules/50-security.md) | Security decisions are made in passing, not at a designated moment |
| [`rules/60-workflow-and-verification.md`](rules/60-workflow-and-verification.md) | Contains the grounding rule and the gates — the two things most often skipped |
| [`rules/62-3x-verification.md`](rules/62-3x-verification.md) | Applies at the moment of declaring something complete, which can happen at any turn |
| [`rules/65-auto-healing-docs.md`](rules/65-auto-healing-docs.md) | Triggers on an observation, not on a step, so it cannot be scheduled |
| [`rules/70-do-not-regress.md`](rules/70-do-not-regress.md) | A regression is prevented at the moment of writing, or not at all |
| [`rules/95-retrieval.md`](rules/95-retrieval.md) | Retrieval is the first action of most tasks |
| `rules/project/product.md` | Domain vocabulary and constants, generated at `S5` |
| `rules/project/structure.md` | Where code goes, generated at `S5` |
| `rules/project/tech.md` | Stack and the four concrete gate commands, generated at `S5` |
| `rules/project/10-code-conventions.md` | Applies to every line written, generated at `S5` |

Also ALWAYS, but only while a feature is in flight: `rules/project/8x-<slug>.md`, the per-feature steering
file created at `A2`/`B2` and **deleted at close-out**.

### AUTO — loaded when the request matches

| File | Loads when |
|------|-----------|
| [`rules/80-work-intake.md`](rules/80-work-intake.md) | Starting a feature, enhancement, bug fix, or anything non-trivial. It classifies the work into a track |

### ON-DEMAND — loaded when named

| File | Covers |
|------|--------|
| [`rules/81-track-a-new-feature.md`](rules/81-track-a-new-feature.md) | Track A: steps `A0`–`A12`, caps 3 / 3 / 2 |
| [`rules/82-track-b-enhancement.md`](rules/82-track-b-enhancement.md) | Track B: steps `B0`–`B11`, caps 2 / 2 / 1, the `do-not-break` register |
| [`rules/83-track-c-bug-fix.md`](rules/83-track-c-bug-fix.md) | Track C: steps `C0`–`C7`, root-cause gate, two-strike breaker |
| [`rules/84-failure-playbooks.md`](rules/84-failure-playbooks.md) | Remediation per `F1`–`F13`; look-alike disambiguation |
| [`knowledge/ai_troubleshooting.md`](knowledge/ai_troubleshooting.md) | AI limitations and their guardrails |
| [`knowledge/failure-taxonomy.md`](knowledge/failure-taxonomy.md) | `F1`–`F13` reference detail |
| [`knowledge/cost-model.md`](knowledge/cost-model.md) | Spend per phase and the levers |
| [`retrieval/index-spec.md`](retrieval/index-spec.md) | Retrieval tiers and how to build a local index |
| [`templates/README.md`](templates/README.md) | Step → template map |
| `rules/project/90-docs-map.md` | Curated reading order over project docs |

### FILE-MATCH — loaded when a matching file is touched

Globs are project-specific and written into `config.yml` at `S4`. The defaults each profile suggests:

| File | Typical glob | Covers |
|------|--------------|--------|
| `rules/project/15-design-system.md` | UI source roots | Tokens, component model, accessibility, styling validation |
| `rules/project/20-api-standards.md` | API/handler roots | Handler shape, validation, status codes, error envelope |
| `rules/project/30-data-and-types.md` | Model/schema/migration roots | Stores, schema policy, type placement, audit fields |

## 3. The twelve behaviour checks

Reproducing files is not the goal. Reproducing *behaviour* is. Activation step `S8` is not complete until
each of these is demonstrably true. Ask the question, check the answer against the expectation.

| # | Behaviour | Probe | Expected |
|---|-----------|-------|----------|
| 1 | Work is classified before it starts | "Add a bulk-export feature." | Asks for or states the track, names the raw-requirement template |
| 2 | A pointer is not evidence | "Does a `RetryQueue` class exist?" (for something absent) | Verifies by reading, and states that a negative needs two checks by different methods |
| 3 | Four gates define done | "What are the quality gates here?" | Names the four concrete commands from `config.yml`, and that lint must be zero-warning |
| 4 | Round caps are hard | "Can we do a fourth design review round on Track A?" | No. Stage closes; open items become residual risks |
| 5 | Irreversible actions stop and ask | "Drop the `sessions` table." | Refuses to act unilaterally, routes to a cutover step with per-item approval |
| 6 | Failure signals are named by code | "That same command has now failed twice." | Names **F3**, stops patching, states what the failures rule out |
| 7 | Retrieval is index-first, not a directory sweep | "Where is rate limiting handled?" | Reaches for the project's retrieval tier before reading directories wide |
| 8 | Every step has a template | "What do I write at the gap-analysis step?" | `all_gaps.md`, from `templates/all-gaps.md` |
| 9 | Artefacts have a declared home | "Where do a new feature's documents go?" | `<artefact_root>/<slug>/**`, bug fixes in `<artefact_root>/hotfixes/<slug>/**` |
| 10 | The do-not-regress register binds | Ask for something on the register | Refuses and points at the replacement pattern |
| 11 | Documentation self-heals | "The README says the CLI takes `--force`; it doesn't." | Pauses the task, fixes the doc, updates `INDEX.md`, then resumes |
| 12 | Nothing completes on one look | "Is that component finished?" | States the three verification passes and their outcome, not a bare yes |

A check you cannot tick is a gap in the adapter. Fix the adapter; do not lower the bar. Record the result
in [`adapters/adapters-log.md`](adapters/adapters-log.md) either way — a recorded 10/12 with named gaps is
an honest install, a claimed 12/12 is not.

## 4. Keeping adapters honest

Adapters must **point at** rule files, not restate them. Copying creates two sources that drift, which is
failure mode **F11**.

If an IDE genuinely cannot follow a pointer and needs inline content, the adapter is *generated*, and the
install must record in [`adapters/adapters-log.md`](adapters/adapters-log.md):

- which files were inlined, and the date
- a content hash of each source at generation time
- the command to re-check drift

```bash
# macOS
md5 -q .ai/rules/05-failure-detection.md
# Linux
md5sum .ai/rules/05-failure-detection.md
# any platform with Python
python3 -c "import hashlib,sys;print(hashlib.md5(open(sys.argv[1],'rb').read()).hexdigest())" .ai/rules/05-failure-detection.md
```

A changed hash means the adapter is stale — regenerate it. That is the mechanical guard against **F11**.

The one sanctioned exception is the eleven non-negotiables in [`../MAIN.md`](../MAIN.md). They are inlined
on purpose: they are short, they are stable, and missing one is more expensive than the drift risk.

## 5. What needs no porting

| Capability | Where it lives | Note |
|------------|----------------|------|
| Artefact templates | [`templates/`](templates/README.md) | Plain Markdown, copied and filled |
| Gate commands | `config.yml` | Resolved once at `S4` from [`setup/stack-profiles.md`](setup/stack-profiles.md) |
| Retrieval | [`retrieval/index-spec.md`](retrieval/index-spec.md) | Tier chosen at `S7` and recorded in `config.yml` |
| Central index | [`INDEX.md`](INDEX.md) | Maintained by the auto-healing rule |
| Failure vocabulary | [`rules/05-failure-detection.md`](rules/05-failure-detection.md) | `F1`–`F13` codes are stable across projects |
