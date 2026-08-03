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

**Activation.** `super_saiyan_bann_jo` triggers a nine-step protocol (`S0`–`S8`) that discovers the project by
evidence, discovers the assistant's own instruction mechanism by evidence, installs the payload, generates the
project rule layer, writes an IDE adapter, chooses a retrieval tier, and **proves twelve behaviour checks**
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
