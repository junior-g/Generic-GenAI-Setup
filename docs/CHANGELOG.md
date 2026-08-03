# Changelog

Framework versions. Every installation records the version it came from in its `.ai/config.yml` →
`framework.version`, which is what makes a re-sync decidable.

Versioning:

| Bump | Means | Re-sync needed? |
|------|-------|-----------------|
| **Major** | a rule changed such that existing installations behave differently | yes, and read the notes first |
| **Minor** | rules or templates added; nothing existing changed meaning | optional — adds capability |
| **Patch** | wording, corrections, a new stack profile or IDE row | optional |

Re-syncing refreshes `rules/00`–`95`, the templates, the manifest and the retrieval spec. It **never**
overwrites `rules/project/**`, `config.yml`, `INDEX.md` Zone 2, or `work/**`. Procedure:
[`../.ai/setup/activation-protocol.md`](../.ai/setup/activation-protocol.md#re-activation-and-re-sync).

---

## 1.0.0

Initial release. A generalisation of a working setup from a production codebase — language-agnostic,
IDE-agnostic, no dependencies.

**Activation.** `super_saiyan_bann_jo` triggers a nine-step protocol (`S0`–`S9`) that discovers the project by
evidence, discovers the assistant's own instruction mechanism by evidence, installs the payload, generates the
project rule layer, writes an IDE adapter, chooses a retrieval tier, and **proves fourteen behaviour checks**
before reporting success.

**Contract.** Four load modes — ALWAYS, AUTO, ON-DEMAND, FILE-MATCH — each with a defined fallback, so the rule
set reproduces in any tool. Adapters reference rule files rather than copying them; inlined content carries a
content hash so drift is mechanically detectable.

**Language independence.** The four gates are *roles* (LINT, STATIC, BUILD, TEST), not commands. Concrete
commands are mapped once, per project, in `config.yml`, with starting profiles for eleven ecosystems. A gate
with no tool in a given language is substituted and declared, never deleted.

**Rules.** Thirteen universal files, copied verbatim and never edited per project:

| | |
|---|---|
| `00-agent-contract` | grounding, reversibility, scope, honesty, completion, tooling |
| `05-failure-detection` | signal → `F1`–`F13` → first move; the hard-stop list |
| `50-security` | secrets, validation, injection, authorisation, dependencies, privacy |
| `60-workflow-and-verification` | the grounding rule, the four gates, resuming |
| `62-3x-verification` | three passes, discard triggers, proportionality |
| `65-auto-healing-docs` | pause, fix, index, resume — bounded to depth 1 |
| `70-do-not-regress` | universal register plus a per-project part |
| `80-work-intake` | classify A / B / C / T before starting |
| `81` `82` `83` | the three tracks, with hard round caps |
| `84-failure-playbooks` | containment, correction, prevention per `F`-code |
| `95-retrieval` | index-first; no wide directory reads |

**Project layer.** Eight templates generated at activation — product, structure, tech, conventions, design
system, API standards, data and types, docs map. Generated only where the project has that layer; omissions are
recorded as decisions.

**Templates.** Nineteen artefact templates covering every step of every track, plus seven task-type formats
(UI, backend, database, infrastructure, data/ML, integration) that add discipline-specific checks on top of the
common blocks.

**Knowledge.** `ai_troubleshooting.md` explains the eight failure classes mechanically and names the guardrail
against each. `failure-taxonomy.md` is the `F1`–`F13` reference with per-code diagnostics and boundaries.
`cost-model.md` ranks the levers by saving, without inventing figures.

**Retrieval.** Three tiers with a decision procedure. No engine is bundled — that would require a runtime a
portable framework cannot assume. Tier 2's full specification is provided for projects large enough to need it.

### Design decisions worth recording

| Decision | Reasoning |
|----------|-----------|
| No escape hatch on round caps | a cap with an exception clause is a suggestion, and **F7** finds the exception every time |
| Negatives need two checks by *different* methods | two searches share a failure mode. The two most expensive wrong claims in this framework's history were both scoped-search negatives |
| Root cause written before the first edit | the fix report existing before the fix is the only enforcement that cannot be satisfied retroactively |
| Universal rules never edited in place | it is what makes a re-sync safe, and therefore what makes the framework maintainable across installations |
| Non-negotiables inlined in `MAIN.md` | a deliberate exception to reference-don't-copy: short, stable, and expensive to miss under positional attention decay |
| No bundled retrieval engine | a runtime dependency would break the portability the framework exists to provide |
| Activation must prove behaviour, not files | a rule file that exists and never loads has changed nothing |

### Known limitations at 1.0.0

Full list with mitigations: [`rationale.md`](rationale.md) §5.

- Reading the right file and misreading it is not caught by grounding.
- A hard cap can close a stage with a real blocker still unfound.
- A contract existing only in a consumer's expectations has nothing to search for.
- Migration correctness cannot be verified without production-like data.
- The IDE matrix is a set of hypotheses; tool conventions change faster than this file.
- Nothing enforces process compliance — the artefacts are the evidence, and a missing one is visible.

---

## Adding an entry

```markdown
## <version> — <date>

<One paragraph: what changed and why.>

### Changed
| File | Change | Re-sync required |
|------|--------|------------------|

### Added
| File | Purpose |
|------|---------|

### Removed
| File | Why | Replaced by |
|------|-----|-------------|

### Migration notes for existing installations
<What an installation on the previous version must do. "Nothing" is a valid answer, stated explicitly.>
```

Removing a rule needs the same care as removing code: say what replaced it, and whether an installation that
still follows the old rule is now wrong or merely out of date.

---

## 1.1.0

**Adds the enforcement layer.** v1.0.0 described the process completely and never checked it. Two installs
completed with no index built and no track followed; both would have scored 14/14 on behaviour, because nothing
asked about either. This release closes that.

The gap was structural, not a wording problem: every rule was an obligation, and no moment in a session or an
install ever required one to be produced.

### Added

| File | Purpose |
|------|---------|
| `.ai/rules/01-session-preflight.md` | **ALWAYS.** The framework's one enforcement moment. A five-line preflight block — track, stage and resume read, gates, retrieval tier, pins — before the first file edit of any session. Three of those lines can halt the work |
| `.ai/setup/install-audit.md` | **Activation step `S9`.** The artefact counterpart to the behaviour probes: 69 rows across 7 blocks, each verified by opening a file. Four rows are blocking |
| `.ai/retrieval/build-index.md` | **The missing indexing procedure.** Six passes that populate `INDEX.md` Zone 2, a deliverable defined per tier, and the requirement to report the tier's limitation to the user |

### Changed

| File | Change | Re-sync required |
|------|--------|------------------|
| `.ai/setup/activation-protocol.md` | `S7` rewritten: every tier now has a named deliverable, and choosing tier 0 no longer looks like a decision to build nothing. New `S9` install-audit step. Protocol is now `S0`–`S9` | yes |
| `.ai/manifest.md` | ALWAYS set is nine universal files, not eight. Behaviour checks 12 → **14**: #13 indexing exists and is used, #14 preflight before the first edit | yes |
| `.ai/adapters/README.md` | Adapter content contract gains item **11** — the preflight must be named, or the enforcement moment never loads | yes |
| `.ai/adapters/adapters-log.md` | Entries now record two scores: behaviour /14 and the install-audit blocks | yes |
| `.ai/rules/95-retrieval.md` | An empty `INDEX.md` is now named as an incomplete install, not a reason to read directories wide | no |
| `.ai/rules/05-failure-detection.md` | Two signal rows: editing without a preflight, and reaching for a directory read with no known index | no |
| `.ai/rules/00-agent-contract.md` · `80-work-intake.md` | Point at the preflight as where the track becomes visible | no |
| `.ai/knowledge/ai_troubleshooting.md` | New §9 "Installed but not followed" — the three-layer diagnostic: loaded, reachable, required | no |
| `MAIN.md` · `README.md` · `.ai/INDEX.md` | Reflect `S0`–`S9`, 14 checks, the nine-file ALWAYS set, and the three new files | yes |

### Migration notes for existing installations

An installation on 1.0.0 is missing the enforcement layer and will exhibit the reported symptoms. To upgrade:

1. Re-sync the universal payload: `rules/00`–`95`, `templates/`, `manifest.md`, `retrieval/`, `setup/`.
   `rules/project/**`, `config.yml`, `INDEX.md` Zone 2 and `work/**` are **never** overwritten.
2. **Add `01-session-preflight.md` to the adapter's ALWAYS list.** Without this the upgrade changes nothing —
   the rule will be present and never loaded.
3. Confirm the adapter names `80-work-intake.md`. On most tools AUTO is unsupported, so an unnamed work-intake
   silently never loads and work is never classified.
4. Run `.ai/setup/install-audit.md` in full. Expect A5.2 (`INDEX.md` Zone 2 populated) to fail on installs that
   chose tier 0 under 1.0.0 — that is the indexing gap. Fix it with `retrieval/build-index.md` §2. **Do not
   change tier**; the tier was probably right and the deliverable was skipped.
5. Re-run `S8` with the two new probes, then `S9`. Log both scores as a new `adapters-log.md` entry referencing
   the old one.

### Design decisions worth recording

| Decision | Reasoning |
|----------|-----------|
| The preflight is a *stop*, not a formality | three of its five lines can halt the work. A checkpoint that cannot fail is a comment |
| `S8` and `S9` stay separate | behaviour and artefacts fail differently. Merging them is how an empty index passes an install |
| Tier 0 gets a mandatory deliverable | "no engine" was being read as "no indexing". Tier 0 is a hand-built index, and saying so removes the ambiguity |
| The tier's *limitation* must be reported | invisible work gets reported as missing. Two sentences at `S7` prevent a support round |
| Fix the adapter, not the rules | an unreferenced rule and a wrong rule look identical from outside. Rewriting the rule produces a second version of it — **F11** |
