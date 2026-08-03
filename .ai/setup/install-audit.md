# Install Audit

**Activation step `S9`.** The artefact counterpart to the behaviour checks.

[`../manifest.md`](../manifest.md) §3 asks *does the agent behave correctly?* This asks *does the deliverable
exist, and is it filled?* They fail differently, and an install can pass one while failing the other — which
is exactly how an install ends up scoring 14/14 with an empty index and an unfilled config.

Run it at the end of activation, and again any time an installed framework appears not to be working.

> **Every row is checked by opening the file.** A row ticked because the path exists is not an audit — a
> template copied and never filled exists just as convincingly as a completed one.

---

## How to score

| | |
|---|---|
| ✅ | present, filled, and verified by reading |
| ⚠️ | present but incomplete — say which part |
| ❌ | absent, or still a template |
| ➖ | deliberately omitted, with the reason recorded |

**A ❌ or ⚠️ is not a failed install. An unreported ❌ is.** Report the real score and fix the rows that matter.

---

## A1 — Payload installed

| # | Check | How | Result |
|---|-------|-----|--------|
| 1.1 | `.ai/` exists at the project root | list it | |
| 1.2 | All universal rules present — `00`–`95` | count the files against [`../INDEX.md`](../INDEX.md) | |
| 1.3 | `.ai/templates/` complete — 19 artefact templates + 7 task formats | count | |
| 1.4 | `.ai/work/` and `.ai/work/hotfixes/` exist as the artefact root | list | |
| 1.5 | Copied, not symlinked — the project stands alone without the framework directory | check the entries are real files | |
| 1.6 | Generated index artefacts are gitignored *(tier 2 only)* | read the ignore file | |

## A2 — `config.yml` complete 🔴

**The highest-value block in this audit.** An unfilled `config.yml` breaks every rule that refers to a gate or
a path — which is most of them — and it fails silently, because the rules still read as sensible prose.

| # | Check | Result |
|---|-------|--------|
| 2.1 | `.ai/config.yml` exists — not just `config.example.yml` | |
| 2.2 | **No unreplaced `<angle-bracket>` value anywhere in it** | |
| 2.3 | All four `gates.*.command` values are real commands | |
| 2.4 | Each gate's `notes` says it was **verified**, with the result | |
| 2.5 | `gates.lint.zero_warnings` set deliberately | |
| 2.6 | `gates.test.single_run_flag` set — the test gate terminates | |
| 2.7 | `paths.*` point at directories that exist | |
| 2.8 | `paths.file_match.*` globs match real files, or are empty because the layer does not exist | |
| 2.9 | `retrieval.tier` set, with a written `rationale` | |
| 2.10 | `ide.*` filled, including `how_confirmed` | |
| 2.11 | `unresolved` block present — populated or explicitly empty | |

```bash
# 2.2 — unreplaced placeholders. Any output is a finding.
grep -nE '<[a-z][^>]*>' .ai/config.yml

# 2.3 / 2.7 — do the commands and paths actually resolve?
grep -E 'command:|_root:|module:' .ai/config.yml
```

**Then run all four gate commands.** A command in `config.yml` that has never been executed is a guess
(**F5**), and the first real gate run will expose it.

## A3 — Project rule layer generated

| # | File | Check | Result |
|---|------|-------|--------|
| 3.1 | `product.md` | real actors, real vocabulary, no `<...>` | |
| 3.2 | `structure.md` | the tree matches the actual tree; placement rules filled | |
| 3.3 | `tech.md` | versions from the manifest; the four gates match `config.yml` **exactly** | |
| 3.4 | `10-code-conventions.md` | conventions cited from real files | |
| 3.5 | `15-design-system.md` | filled, or ➖ with the reason in `project/README.md` | |
| 3.6 | `20-api-standards.md` | filled, or ➖ with the reason | |
| 3.7 | `30-data-and-types.md` | filled, or ➖ with the reason | |
| 3.8 | `90-docs-map.md` | filled, or ➖ because the project has no docs | |
| 3.9 | `70-do-not-regress.md` Part 2 | project rows added, or explicitly none found | |
| 3.10 | `project/README.md` | generation log and omissions filled | |

| # | Cross-check | Result |
|---|-------------|--------|
| 3.11 | No `.template.md` header block survives in a generated file | |
| 3.12 | Claims carry `path:line` pointers | |
| 3.13 | `tech.md` gate commands and `config.yml` gates **agree** — a mismatch is **F11** | |

```bash
# 3.11 — a surviving template header means the file was copied, not generated
grep -rl 'TEMPLATE — ' .ai/rules/project/*.md | grep -v '\.template\.md'

# 3.1–3.8 — unfilled fills in generated rules
grep -rnE '<[a-z][^>]*>' .ai/rules/project/*.md | grep -v '\.template\.md'
```

## A4 — Adapter written

| # | Check | Result |
|---|-------|--------|
| 4.1 | The adapter file exists at the path `config.yml` → `ide.adapter_files` names | |
| 4.2 | All ten content items present — [`../adapters/README.md`](../adapters/README.md) | |
| 4.3 | The four gate commands appear concretely, not as a pointer | |
| 4.4 | The ALWAYS list is complete — **nine** universal files plus four generated | |
| 4.5 | ON-DEMAND and FILE-MATCH files listed with purpose and glob | |
| 4.6 | The grounding rule stated, including two-checks-for-a-negative | |
| 4.7 | Hard stops stated | |
| 4.8 | **`01-session-preflight.md` referenced** — without it the process is aspirational | |
| 4.9 | Rules referenced, not copied — or hashes recorded in the log | |
| 4.10 | No unreplaced `<angle-bracket>` value | |
| 4.11 | Any pre-existing instruction set left in place, not deleted | |

Item 4.8 is the one most often missed, and it is the one whose absence produces "the framework is installed
but nothing follows it".

## A5 — Indexing exists 🔴

**The row that catches the most common silent omission.** Procedure:
[`../retrieval/build-index.md`](../retrieval/build-index.md).

| # | Check | Result |
|---|-------|--------|
| 5.1 | `retrieval.tier` recorded with a rationale | |
| 5.2 | `.ai/INDEX.md` Zone 2 **is populated** — not the pristine placeholder rows | |
| 5.3 | One Zone 2 row per project document, each with a status | |
| 5.4 | Every entry point, source root and config location reachable from `INDEX.md` | |
| 5.5 | The active-work table exists and is empty, ready for the first slug | |
| 5.6 | Documentation debt found at `S1` recorded | |
| 5.7 | Zone 2 paths resolve — no row pointing at nothing | |
| 5.8 | The tier and what it does **and does not** provide was reported to the user | |

Tier 2 only:

| # | Check | Result |
|---|-------|--------|
| 5.9 | Build command runs and reports counts | |
| 5.10 | One real query run, output pasted | |
| 5.11 | Returned pointers resolve to real lines | |
| 5.12 | A query for something absent returns empty, not a spurious match | |
| 5.13 | Self-test added to `gates.extra` | |
| 5.14 | Generated artefacts gitignored | |

```bash
# 5.2 — pristine placeholder text still present means Zone 2 was never filled
grep -n 'generated at `S7`\|one row per project document, with a status' .ai/INDEX.md

# 5.7 — Zone 2 rows pointing at nothing
grep -oE '\]\(([^)]+)\)' .ai/INDEX.md | tr -d ']()' | while read p; do
  [ -e "$p" ] || [ -e ".ai/$p" ] || echo "MISSING: $p"; done
```

**If 5.2 is ❌ the project has no working index**, whatever `retrieval.tier` claims. That is the gap most
often reported as "the setup missed the indexing logic", and it is fixed by running
[`../retrieval/build-index.md`](../retrieval/build-index.md), not by changing tier.

## A6 — Process is reachable

Not "is it documented" — **can the agent get to it from a cold start?**

| # | Check | Result |
|---|-------|--------|
| 6.1 | `01-session-preflight.md` is in the ALWAYS set and named in the adapter | |
| 6.2 | `80-work-intake.md` reachable — AUTO natively, or named in the ALWAYS adapter | |
| 6.3 | The three track files reachable by name | |
| 6.4 | `templates/README.md` reachable, so a step maps to a template | |
| 6.5 | Artefact root exists and matches `config.yml` | |
| 6.6 | Task formats reachable from the task templates | |

Row 6.2 is the second cause of "it did not follow the feature steps". **AUTO is unsupported on most tools**
([`../adapters/ide-matrix.md`](../adapters/ide-matrix.md)), so work-intake silently never loads unless the
adapter names it. Check the adapter text, not the manifest's intent.

## A7 — Behaviour

The fourteen probes in [`../manifest.md`](../manifest.md) §3. Recorded in
[`../adapters/adapters-log.md`](../adapters/adapters-log.md).

| | |
|---|---|
| Score | <n>/14 |
| Probes actually run, not assumed | ☐ |

---

## Result

| Block | Score | Blocking gaps |
|-------|-------|---------------|
| A1 payload | /6 | |
| A2 config 🔴 | /11 | |
| A3 project rules | /13 | |
| A4 adapter | /11 | |
| A5 indexing 🔴 | /8 (+6 tier 2) | |
| A6 process reachable | /6 | |
| A7 behaviour | /14 | |

### Blocking — the install is not usable until these are fixed

| Row | Gap | Why it blocks |
|-----|-----|---------------|
| A2 | `config.yml` incomplete | no gate commands means no definition of done |
| A4.8 / A6.1 | preflight not in the adapter | the process will not be followed |
| A5.2 | `INDEX.md` Zone 2 empty | no index; every task starts with a directory sweep |
| A6.2 | work-intake unreachable | work is never classified |

### Non-blocking

Everything else. Record it, say what is missing, and let the user decide.

## Reporting

Score, blocking gaps, what you fixed, what remains. Append to
[`../adapters/adapters-log.md`](../adapters/adapters-log.md).

> Install audit: A1 6/6 · A2 11/11 · A3 11/13 (`15-design-system.md`, `20-api-standards.md` ➖ — no UI, no
> HTTP surface; recorded in `project/README.md`) · A4 11/11 · A5 8/8 tier 0 · A6 6/6 · A7 14/14.
> No blocking gaps. Fixed during the audit: `INDEX.md` Zone 2 was empty — populated from the real tree,
> 14 rows.

A claimed clean audit with no per-block numbers is **F8**. The numbers are the point.
