# Retrieval Specification

**Load mode: ON-DEMAND.** Read at activation step `S7` to choose a tier, or when a project outgrows the tier it
has.

The rule that governs day-to-day use is [`../rules/95-retrieval.md`](../rules/95-retrieval.md). This file is
the *decision* — which tier a project needs — and the *build spec* for the one tier that requires building.

> **The framework ships no retrieval engine.** It ships the decision procedure and the specification. A
> pre-built engine would need a language, a runtime and dependencies, which is exactly what a portable
> framework cannot assume. Tiers 0 and 1 need nothing at all, and most projects should stop there.

---

## 1. The three tiers

| Tier | What it is | Cost to set up | Needs |
|------|-----------|----------------|-------|
| **0** | [`../INDEX.md`](../INDEX.md) plus exact-match search over the tree | none | nothing |
| **1** | Tier 0 plus the IDE's or editor's own semantic search | none | an IDE that has one |
| **2** | Tier 0 plus a purpose-built local retrieval index over docs and source | roughly a day, once | a scripting runtime already present in the project |

Every tier includes tier 0. **[`../INDEX.md`](../INDEX.md) is never optional** — it is the only component that
survives every tier choice, and it is the auto-healing target.

## 2. Choosing a tier

Answer in order. Stop at the first tier that fits.

| # | Question | If yes |
|---|----------|--------|
| 1 | Fewer than roughly 200 source files, or fewer than 30 documents? | **Tier 0.** An index over a corpus you can hold in your head is cost with no return |
| 2 | Does your IDE have working semantic search over the repository? | **Tier 1.** Free, already installed, good enough |
| 3 | Are answers regularly wrong because search hits the wrong layer of a large codebase? | **Tier 2** is worth considering |
| 4 | More than roughly 500 source files **and** more than 50 documents **and** multiple distinct domains? | **Tier 2** |
| 5 | Do agents repeatedly read directories wide because targeted search does not find things? | **Tier 2** — that is **F4** and it is costing more than the index would |

**Do not default to tier 2 because it is the most sophisticated.** That is **F7** and **F12** together. The
tier decision is recorded in `config.yml` and is reversible; starting at 0 and moving up when the pain is real
is the correct order.

Record in `config.yml`:

```yaml
retrieval:
  tier: "0"
  rationale: "38 source files, 6 documents. INDEX.md plus exact search covers it. Revisit past ~200 files."
  index_build_command: null
  index_query_command: null
  index_stats_command: null
  index_artefact_path: null
  rebuild_trigger: "n/a at tier 0"
```

## 3. Tier 0 — index plus exact search

**How to use it well:**

1. [`../INDEX.md`](../INDEX.md) first. For "where does X live", it is often the whole answer.
2. `project/90-docs-map.md` for the curated reading order.
3. Exact search, **scoped to the roots that could plausibly contain it** — from `config.yml` → `paths`.
4. Search for the **symptom string** when debugging: the error text, the log line, the exact identifier.
5. Read the whole enclosing unit, not the three matching lines.

**Its real limitation:** it cannot answer a conceptual question. "How does session expiry work" finds nothing
unless something is literally named that. The workaround is to derive query terms from the nouns you expect in
the source, and to keep `INDEX.md` genuinely current so conceptual questions land there instead.

## 4. Tier 1 — IDE semantic search

Same as tier 0, plus the editor's own semantic search for conceptual questions.

| # | Rule |
|---|------|
| 1 | Verify it actually indexes this repository before recording tier 1 — a feature that exists and is not enabled is tier 0 |
| 2 | Note whether it respects the ignore file. An index including build output returns noise |
| 3 | Its results are pointers, like everything else. Read the file |
| 4 | If it sends code to a remote service, that is a data-handling decision — flag it to the user rather than assuming it is fine |

Record the exact feature name in `config.yml` → `retrieval.rationale`, so a later session knows what "tier 1"
meant here.

## 5. Tier 2 — a local retrieval index

Build only when §2 says so. This section is the specification.

### 5.1 Design constraints

| Constraint | Why |
|------------|-----|
| **Local and zero-cost** — no API key, no external service | a framework requirement, and it removes the data-handling question entirely |
| **No database** — plain files on disk | portable, diffable, trivially deletable |
| **Generated output gitignored** | it is derived; a fresh clone rebuilds it |
| Built in a language the project already has | no new toolchain |
| Build in seconds, not minutes | an index that is slow to build is never rebuilt, and a stale index is worse than none |
| Query answers in under a second | anything slower gets skipped under pressure |

### 5.2 Corpus and chunking

**Include:** documentation, source, configuration, schema and migration files, the framework's own `.ai/**`.
**Exclude:** dependencies, build output, generated code, binaries, lockfiles, the index's own artefacts.

| Decision | Recommendation | Why |
|----------|---------------|-----|
| Chunk boundary | semantic units — a function, class, heading section — not fixed character counts | a chunk cut mid-function is unusable as an answer |
| Chunk size | a few hundred to ~1500 characters | small enough to be precise, large enough to stand alone |
| Overlap | a small overlap between adjacent chunks | so a boundary does not hide the answer |
| Parent retrieval | store a **parent section** per chunk; retrieve small, return large | matching precisely and reading usefully are different needs |
| Line numbers | **mandatory** — `path:startLine-endLine` on every chunk | the output must be a pointer you can open. This is the single most important property |

Metadata per chunk: file path, start and end line, kind (doc / source / config / schema), domain, symbol name
where applicable, and a content hash for change detection.

### 5.3 Domain partitioning

Partition the corpus so a query opens only the partitions it needs. This is where the cost saving comes from —
a query reads a fraction of the corpus rather than all of it.

Derive partitions from the **project's own structure**, not from a fixed list. A reasonable starting set:

| Partition | Contains |
|-----------|----------|
| `architecture` | layout, composition, rendering, design system, structural docs |
| `engineering` | APIs, data models, build, deploy, conventions |
| `security` | auth, roles, sessions, permissions, compliance |
| `domain` | the project's own subject matter — one partition per major area |

Routing is automatic from the query terms, with an explicit override available (`domain:security …`). Provide
a routing-only command so the decision can be checked without loading anything heavy.

### 5.4 Retrieval strategy

| Engine | Use for | Mechanism |
|--------|---------|-----------|
| **hybrid** (default) | most questions | dense similarity + lexical scoring, results merged |
| **keyword** | exact identifiers, constants, superseded values | lexical only |
| **summary** | broad conceptual questions | a hierarchy of section summaries, searched top-down |
| **graph** | "what connects to what" | an import and reference graph |

Two things matter more than the choice of engine:

- **A local embedder has limited vocabulary generalisation.** Exact-term and vocabulary-overlapping queries
  work; heavy paraphrase does not. Say so in the tool's own help text, so a user rephrases rather than
  concluding the code is absent.
- **Lexical scoring is not optional.** Identifiers, constants and error strings are exactly what people search
  for, and dense similarity alone is poor at them.

### 5.5 Required commands

Four, named consistently, recorded in `config.yml`:

| Command | Does | Must output |
|---------|------|-------------|
| `build` | full rebuild, resetting the store | file and chunk counts per partition, and elapsed time |
| `stats` | reports the current state | existence, freshness, counts, and the build timestamp |
| `query` | answers a question | ranked `path:startLine-endLine` pointers with scores |
| `route` | shows the routing decision only | which partitions a query would open, loading nothing heavy |

Useful flags: assembled parent context · machine-readable output · a wider result count · bypass the cache.

`route` earns its place: it lets you check where a question will land before paying for the query, which is the
cheapest possible fix for a query that returns nothing useful.

### 5.6 Freshness

The failure mode that makes an index actively harmful: it answers confidently about code that changed.

| Rule |
|------|
| `stats` reports the build timestamp and flags staleness against the newest source file |
| Rebuild after each completed section, before querying again |
| The index does not see uncommitted edits — say so in the tool's help |
| Cached answers and chunk identifiers are tied to a build and dropped on rebuild |
| Changing chunking or embedding parameters invalidates the store — rebuild with a reset |
| A pointer into a file that no longer exists means stale; rebuild, and check whether `INDEX.md` needs healing too |

### 5.7 Self-test

A tier-2 index is a piece of software in the project, so it gets its own gate. Add it to `config.yml` →
`gates.extra`, required whenever the index tooling changes.

Minimum contract checks:

| # | Check |
|---|-------|
| 1 | Build produces the expected artefacts for every partition |
| 2 | Chunk line numbers resolve to real lines in real files |
| 3 | A known symbol is retrievable by exact name |
| 4 | A known conceptual question returns the right file in its top results |
| 5 | Routing sends a domain-specific query to the right partition |
| 6 | Explicit domain and engine overrides are honoured |
| 7 | A rebuild after a file change reflects the change |
| 8 | A query for something genuinely absent returns empty rather than a spurious match |
| 9 | Cache invalidation on rebuild works |
| 10 | Build completes within its stated time budget |

Check 8 is the important one, and it is the check most likely to be omitted: an index that always returns
*something* trains you to trust a spurious match, which is **F5** with tooling assistance.

## 6. Moving between tiers

**Upgrading** is additive. Tier 0's `INDEX.md` remains the entry point; the new engine is an extra lookup. Run
both in parallel briefly and compare — if the index does not beat scoped search on real questions, the corpus
did not need it.

**Downgrading** is a removal: it deletes tooling and a `gates.extra` entry. That goes through a cutover step
with approval (**F6**), not a tidy-up.

Either way, update `config.yml` → `retrieval` and say why in the rationale. A tier recorded without a reason
gets changed back by the next session.

## 7. What no tier fixes

| Limitation | Consequence |
|------------|-------------|
| **A pointer is not evidence** | every tier returns pointers. The read is what establishes the fact |
| **An empty result is ambiguous** | "absent" and "badly queried" look identical. That is why a negative needs two checks by different methods |
| It cannot see uncommitted edits | rebuild, or state that results predate your changes |
| It indexes what exists, not what is true | a stale document indexes perfectly and answers wrongly. Auto-healing is the fix |
| It cannot find an undocumented, unnamed convention | three call sites read by hand is the only method |
| It cannot find a contract that exists only in a consumer's expectations | ask the user, then verify |
