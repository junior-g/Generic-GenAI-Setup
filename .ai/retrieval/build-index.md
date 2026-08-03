# Building the Index

**Activation step `S7`.** The procedure that produces the project's index. Run it at install, and again
whenever [`../setup/install-audit.md`](../setup/install-audit.md) row A5.2 comes back ❌.

[`index-spec.md`](index-spec.md) decides *which tier*. This file is *how to actually build it*.

> **Every tier has a deliverable.** Tier 0 is not "no indexing" — it is a hand-built index that must exist,
> be populated from the real tree, and be kept current. An install that chose tier 0 and wrote nothing has not
> implemented tier 0; it has skipped `S7`.

That distinction is the single most common activation failure, and it is why this file exists separately from
the specification.

---

## 1. What the deliverable is, per tier

Say this to the user when you record the tier, so a tier-0 choice is a visible decision rather than a silent
omission.

| Tier | You must produce | You do **not** get |
|------|------------------|--------------------|
| **0** | `.ai/INDEX.md` Zone 2, fully populated from the real tree — §2 below | conceptual search. "How does session expiry work" finds nothing unless something is named that |
| **1** | All of tier 0, plus the IDE's semantic search verified as actually indexing this repo | nothing extra to build, but §2 is still mandatory |
| **2** | All of tier 0, plus a working local retrieval engine — build, stats, query, route, self-test | nothing; this is the full capability |

**Tier 0's Zone 2 is mandatory at every tier.** The retrieval engine answers "where is this code"; `INDEX.md`
answers "what exists and where does it live", and no engine replaces it because it is also the auto-healing
target.

## 2. Populating Zone 2 — mandatory at every tier

Six passes. Each produces rows in `.ai/INDEX.md` Zone 2.

### Pass 1 — documents

Every document in the project, from `config.yml` → `paths.docs_root` plus root-level Markdown.

```bash
find . -maxdepth 3 -iname '*.md' -not -path '*/node_modules/*' -not -path '*/.git/*' \
  -not -path './.ai/*' -not -path '*/vendor/*' -not -path '*/target/*' | sort
```

**Open each one.** The status column is a judgement you cannot make from a filename.

| Doc | Covers | Status | Last verified |
|-----|--------|--------|---------------|
| `README.md` | setup, the four commands | ⚠️ stale — names `make test`, no Makefile exists | <date> |
| `docs/architecture.md` | service boundaries, data flow | current | <date> |

| Status | Means |
|--------|-------|
| current | read, and it matches the code |
| stale | read, and something in it is now wrong — name what |
| partial | covers the happy path only |
| missing | referenced somewhere but does not exist |

A `stale` row is documentation debt, recorded here (Pass 6), **not fixed now** — activation is not the moment
to rewrite the project's docs (**F7**).

### Pass 2 — entry points

Where execution begins. An agent that cannot find these cannot reason about initialisation, configuration
loading, or startup failure.

| Entry point | File | Starts |
|-------------|------|--------|
| HTTP server | `src/server.ts:1` | the API |
| CLI | `cmd/tool/main.go:12` | the command-line tool |
| Worker | `src/jobs/worker.py:1` | the queue consumer |

From `structure.md`, which `S5` already established — copy, do not re-derive.

### Pass 3 — layers

One row per layer, so "where does this go" is answerable without a search.

| Layer | Root | Contains | Rule that governs it |
|-------|------|----------|---------------------|
| UI | `src/components/` | | `project/15-design-system.md` |
| API | `src/api/` | | `project/20-api-standards.md` |
| Data | `src/models/`, `migrations/` | | `project/30-data-and-types.md` |

A layer the project does not have gets no row, and its absence is recorded in `project/README.md` as a
decision.

### Pass 4 — the things asked for most

The highest-value pass, and the one an agent skips because it is judgement rather than mechanism. Six rows
that answer the questions every task starts with.

| Question | Answer | Path |
|----------|--------|------|
| Where are constants? | | `config.yml` → `paths.constants_module` |
| Where is environment read? | | `paths.env_module` |
| Where does authorisation happen? | | |
| Where is the schema defined? | | |
| Where do tests live, and how are they named? | | |
| What is generated and must not be hand-edited? | | |

### Pass 5 — work tables

Two tables, both starting empty. They are the resume point once work begins.

| Slug | Track | Stage | Artefacts | Status |
|------|-------|-------|-----------|--------|
| _(empty — ready for the first slug)_ | | | | |

| Slug | Track | Shipped | Lasting record |
|------|-------|---------|----------------|
| _(empty)_ | | | |

### Pass 6 — documentation debt

Everything Pass 1 marked `stale`, `partial` or `missing`.

| Gap | Severity | Raised by | Plan |
|-----|----------|-----------|------|
| `README.md` names `make test`; no Makefile | 🟡 | install `S7` | heal when work next touches setup docs |

Recorded, not fixed. The auto-healing rule picks each one up the first time real work touches it.

### Delete the pristine markers

The framework copy ships Zone 2 with placeholder rows and a `Zone 2 status: not generated` line. **Remove
them.** A surviving placeholder is how audit row A5.2 fails, and it is the difference between an index and an
index-shaped file.

## 3. Tier 1 — verify, do not assume

| # | Check | Why |
|---|-------|-----|
| 1 | The semantic search actually indexes **this** repository | a feature that exists and is not enabled is tier 0 |
| 2 | It respects the ignore file | an index including build output returns noise |
| 3 | Run one real conceptual query and read the result | an unverified capability is not a capability |
| 4 | Establish whether it sends code to a remote service | that is a data-handling decision — flag it to the user |

Record the exact feature name in `retrieval.rationale`, so a later session knows what "tier 1" meant here.

## 4. Tier 2 — building the engine

Full specification: [`index-spec.md`](index-spec.md) §5. Order of work:

| # | Step | Done when |
|---|------|-----------|
| 1 | Choose the language — one the project **already has** | no new toolchain added |
| 2 | Define the corpus: include and exclude globs | build counts match expectation |
| 3 | Chunk on semantic boundaries, with `path:startLine-endLine` on every chunk | pointers resolve to real lines |
| 4 | Partition by domain, derived from this project's structure | `route` sends a query to the right partition |
| 5 | Implement hybrid retrieval — dense **plus** lexical | an exact identifier is findable |
| 6 | Implement `build`, `stats`, `query`, `route` | each produces the output §5.5 requires |
| 7 | Write the ten self-test checks | they pass |
| 8 | Add the self-test to `gates.extra` | it runs when the tooling changes |
| 9 | Gitignore the generated artefacts | a fresh clone rebuilds |
| 10 | Record all four commands in `config.yml` | the preflight can name them |

**Then run a build and one real query, and paste the output.** An index that was never queried is not known to
work. Include the absent-term query — an engine that always returns *something* trains you to trust a spurious
match, which is **F5** with tooling assistance.

## 5. Keeping it current

| Tier | Rebuild trigger |
|------|-----------------|
| 0, 1 | The auto-healing rule, continuously: any document created, changed or found wrong updates its row **in the same turn** |
| 2 | Additionally: rebuild after each completed section, and on a fresh clone |

An index that lags reality is worse than no index, because it is trusted.
[`../rules/65-auto-healing-docs.md`](../rules/65-auto-healing-docs.md) is what keeps Zone 2 honest; without it
Zone 2 is accurate for one day.

## 6. Report the tier and its consequence

At `S7`, tell the user in two sentences: the tier, why, and **what it does not give them**.

> Retrieval: tier 0. 38 source files and 6 documents — an engine would cost a day and save nothing at this
> size. `INDEX.md` Zone 2 is populated with 14 rows covering every document, entry point and layer, so "where
> does X live" is answerable without a search. What you do not get is conceptual search: a question phrased in
> words that appear nowhere in the source will not find anything, and the fix is to phrase queries in the
> project's own terms. Revisit past roughly 200 source files.

That paragraph is what prevents the report of "the setup missed the indexing logic". The work was done; without
the sentence, it is invisible.

## 7. Common failures

| Failure | Symptom | Fix |
|---------|---------|-----|
| Tier chosen, nothing built | Zone 2 still has pristine placeholder rows | run §2. Do not change tier — the tier was probably right |
| Zone 2 lists only documents | "where is authorisation" is unanswerable | Passes 2, 3, 4 — the ones that need judgement |
| Statuses assigned without reading | a `current` row that is wrong | open each document. The status is the point of the row |
| Rows pointing at nothing | a pointer into a moved file | audit row A5.7 |
| Tier 2 built, never queried | build output pasted, no query output | run a query, including one for an absent term |
| Tier recorded, consequence not stated | user reports indexing "missing" when it exists | §6 |
| Zone 2 never updated after the first feature | accurate for one day, trusted for a month | the auto-healing rule, and close-out |
