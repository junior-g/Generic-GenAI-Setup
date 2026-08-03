<!--
TEMPLATE — grounding survey. Copy to <artefact_root>/<slug>/relevant_info.md, delete this header, fill it in.
Written at step A1 / B1. Filled by the AGENT.

THIS IS THE MOST IMPORTANT ARTEFACT IN THE TRACK. Everything downstream is built on it, so a wrong claim
here invalidates work that is itself correct (F1) — the most expensive failure in the taxonomy.

TWO NON-NEGOTIABLE RULES:
  1. Every claim carries path:line AND was verified by opening the file. Not a search hit. Not an inference.
  2. Every NEGATIVE claim needs TWO INDEPENDENT CHECKS BY DIFFERENT METHODS. A scoped search returning
     nothing is not a negative — a search pointed at the wrong place returns the same empty result.

Once this exists, DO NOT RE-READ THE SOURCE during design. Re-read this. If you find yourself going back to
source, either this was written badly or it is not being trusted — fix whichever it is.

Sections are not deleted. Write None or Not applicable.
-->

# Relevant Info — <slug>

| | |
|---|---|
| Track | A / B |
| Written | <date> |
| Retrieval tier used | <from config.yml> |
| Queries run | <n> |
| Files opened and read | <n> |
| Scope of this survey | <which surfaces; what was deliberately not surveyed> |

## AIM restated

<One or two sentences, from the raw requirement. Here so this file can be read standalone.>

---

## 1. Queries run

The audit trail of how the pointers were found. It shows whether the grounding was targeted or a sweep.

| # | Query | Why this query | Useful? |
|---|-------|----------------|---------|
| 1 | | | yes / no |

<A run of "no" answers means the vocabulary is wrong — rephrase using terms expected in the source rather
than widening the scope.>

## 2. Positive findings

Everything established to exist. **Read** column is not optional.

| # | Finding | Pointer | Read? | Relevance |
|---|---------|---------|-------|-----------|
| 1 | | `path:line` | ✅ | |

For any 🔴-critical finding, quote the line rather than paraphrasing it:

> `path:line` — `<the actual line or lines>`

Reading the right file and misreading it are different failures. The quote catches the second.

## 3. Negative findings — two checks each

**The table that has caught the most expensive mistakes.** One row per "does not exist" or "nothing depends
on this" claim. Both method columns must be filled with *different* methods.

| # | Claim | Method 1 | Result | Method 2 (different) | Result | Confident? |
|---|-------|----------|--------|---------------------|--------|-----------|
| 1 | | | | | | yes / no |

Acceptable methods: an unscoped filename glob · reading the module, route table or barrel that would consume
it · reading the write sites of the data · running the relevant tests · checking the dependency manifest ·
reading the configuration that would register it.

**Two searches with different terms are not two methods.** They share the same failure mode.

## 4. Existing surfaces in scope

| Surface | Kind | Path | Actor | Notes |
|---------|------|------|-------|-------|
| | | `path:line` | | |

## 5. Data in scope

| Entity | Store | Key fields relevant here | Live records? | Defined at |
|--------|-------|-------------------------|---------------|-----------|
| | | | yes / no / unknown | `path:line` |

<Where "unknown", say what would establish it. Unknown is honest; assumed is not.>

## 6. Reusable components and utilities

What already exists that this work should use rather than rebuild.

| Thing | Does | Path | Reuse verdict |
|-------|------|------|---------------|
| | | `path:line` | reuse / extend / do not reuse because… |

## 7. Conventions observed

Patterns confirmed at three or more call sites. Fewer than three is not a convention — say so.

| Convention | Sites checked | Verdict |
|------------|---------------|---------|
| | `path:line`, `path:line`, `path:line` | consistent / inconsistent — <which pattern is preferred> |

## 8. Constraints discovered

Things that limit the design, found in the code rather than the brief.

| Constraint | Evidence | Implication |
|------------|----------|-------------|
| | `path:line` | |

Include runtime feasibility: what this layer can and cannot reach, in this environment, with this permission.
Assumed feasibility is the design defect that survives until execution.

## 9. Corrections to the raw requirement

Where the brief's stated current behaviour turned out to be wrong. **This being non-empty is a good outcome.**

| # | Brief said | Actually | Evidence | Impact |
|---|-----------|----------|----------|--------|
| 1 | | | `path:line` | |

## 10. Documentation state

Feeds the auto-healing rule.

| Document | Covers | Verdict | Action |
|----------|--------|---------|--------|
| `path` | | current / stale / contradicted / missing | healed now / recorded as debt |

<A contradicted document is healed immediately — this is the cheapest moment to do it, before anything is
built on it.>

## 11. Open unknowns

What the survey could not establish, and what would establish it.

| # | Unknown | What was tried | What would settle it |
|---|---------|----------------|---------------------|
| 1 | | | |

<These become gap-analysis questions. Carrying an unknown forward openly is correct; filling it with a
plausible guess is **F5**.>

## 12. Pinned working set

The small number of files this work genuinely needs. Copied into the feature steering file.

| File | Why pinned |
|------|-----------|
| `path` | |

<More than a handful is not a working set. If the list is long, the work needs phasing.>

## 13. Corrections log

Appended to whenever a claim in this file is later proved wrong. **F1** demands the fix happen here, at the
source, not downstream.

| Date | Claim | Was | Actually | Artefacts re-walked |
|------|-------|-----|----------|--------------------|
| | | | | |
