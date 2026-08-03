# Retrieval

**Load mode: ALWAYS.** Retrieval is the first action of most tasks, and doing it badly is the largest
avoidable cost in a session.

The project's tier and its commands are in `config.yml` → `retrieval`. Tier selection:
[`../retrieval/index-spec.md`](../retrieval/index-spec.md). How the index is built, at every tier:
[`../retrieval/build-index.md`](../retrieval/build-index.md).

**If the project has no index content, the install is incomplete** — not a reason to read directories wide. Run
[`../setup/install-audit.md`](../setup/install-audit.md) block A5 and build it.

---

## The one rule that matters

> **A pointer is not evidence.** Retrieval tells you *where to look*. Opening the file is what makes a claim
> true. A negative result needs two independent checks by different methods.

Everything below is about getting to the right pointer cheaply. None of it substitutes for the read.

## Standard procedure

1. **Check [`../INDEX.md`](../INDEX.md) first.** It is free, it is current if the auto-healing rule has been
   followed, and for "where does X live" it is often the whole answer.
2. **Then the project's retrieval tier** (`config.yml` → `retrieval.tier`):
   - **Tier 0** — exact search over the tree, scoped to the roots that could plausibly contain it.
   - **Tier 1** — the IDE's or editor's semantic search.
   - **Tier 2** — the project's local retrieval index; run its stats command first, rebuild if stale.
3. **Open the returned pointers and read the real file.**
4. **Rebuild the index** (tier 2) after landing a change set, before querying again. An index does not see
   uncommitted edits.

## Query technique

| Do | Don't |
|----|-------|
| Use the exact identifiers, constants and vocabulary you expect **in the source** | Paraphrase the concept in your own words |
| Ask one narrow question per query | Ask a broad question and hope for the best |
| Derive query terms from the nouns in the requirement | Invent domain terms the codebase may not use |
| Widen the result count before widening the scope | Drop the scope and read a directory |
| Search for the *symptom string* when debugging — a log line, an error message | Search for what you think the cause is |
| Follow up a hit by reading the *whole* enclosing unit | Read the three matching lines and infer the rest |

Retrieval with a local or lexical engine rewards vocabulary overlap. If a query returns nothing useful,
**rephrase using terms you expect in the source** before concluding the code is absent. An empty result from
a badly-worded query is indistinguishable from an empty result for something that does not exist — and that
ambiguity is the exact shape of **F5**.

## When to skip retrieval entirely

Do not run a query when:

- The always-on rules already answer it — conventions, gates, structure, constants.
- The file is already in context this session.
- The user named the file.
- It is a trivial lookup you can answer directly.

Running retrieval for something you already know is **F12**, and it costs context you will need later.

## The forbidden move

**Do not read directories wide.** Not "just to get oriented", not at the start of a task, not because the
project is unfamiliar.

A recursive listing of a real repository, or opening every file in a module, fills the working context with
material that is 95% irrelevant and leaves no room for the work. That is **F4**, and it also causes **F10** —
the constraints you were given at the start stop influencing your output because they are now buried.

The correct move when unfamiliar: [`../INDEX.md`](../INDEX.md), then `project/90-docs-map.md`, then targeted
queries derived from the nouns in the task. Depth-limited listings of *specific* directories are fine; the
prohibition is on breadth.

## Grounding budget

Grounding happens **once, early, and narrowly** — Track A step `A1`, Track B step `B1`. The output is a
written survey with `path:line` pointers, and from then on you re-read the *survey*, not the source.

| Signal | What it means | Move |
|--------|---------------|------|
| Opening the same file a third time | it should have been pinned | pin it in the feature steering file (**F4**) |
| Grounding has taken longer than the design will | breadth is out of control | stop, write up what you have, proceed (**F12**) |
| A pin has never been read | it was speculative | prune it |
| Retrieval mid-execution for something the survey covers | the survey is not being used | re-read the survey |

## Pinning

The feature steering file's pinned-context list is the working set: the small number of files this work
genuinely needs, with one line each on why. It is re-pinned at every session start and every section
boundary, and pruned whenever a pin turns out to be dead weight.

A pin list of twenty files is not a working set, it is a directory sweep with extra steps.

## Keeping retrieval honest

| Situation | Action |
|-----------|--------|
| Tier 2 index missing on a fresh clone | build it before first use; the generated index is gitignored |
| Index built before your last change set | rebuild, or state that results predate your edits |
| A query returns a pointer into a file that no longer exists | the index is stale — rebuild, and check whether `INDEX.md` also needs healing |
| A cached answer for a query you have changed the code under | force a fresh pass |
| Two documents answer the same question differently | **F11** — stop, report the conflict, ask which wins |
| The index has no coverage of an area you need | that is documentation debt — record it, then read the source directly |
| `INDEX.md` Zone 2 is empty or still has placeholder rows | `S7` was skipped. Build it: [`../retrieval/build-index.md`](../retrieval/build-index.md) §2 |
