<!--
FORMAT — database / data-layer task. Copy the checklist blocks into the task's block in task_details.md.

Applies to any persistence: relational, document, key-value, graph, event log, or files with meaningful state.
Project specifics come from rules/project/30-data-and-types.md.

DATABASE TASKS ARE THE MOST DANGEROUS IN THE FRAMEWORK. They are the most likely to be irreversible, the most
likely to break at production volume having passed locally, and the hardest to verify without production-like
data.

THE FOUR THINGS THEY FAIL ON MOST:
  1. Records that predate the change. They read as garbage because only the new write path was considered.
  2. Contract-first ordering. Removing the old shape before every reader has migrated.
  3. A migration fast on ten rows that locks the table on ten million.
  4. A backfill that cannot resume, so a failure halfway means starting again.
-->

# Database / Data-Layer Task Format

## Scope

| Applies to | Does not apply to |
|------------|-------------------|
| Schema, migrations, indexes, backfills, queries, constraints, data integrity | Handler logic (backend format), rendering (UI format) |

## 1. Change definition

| | |
|---|---|
| Entity / table / collection | |
| Change | add field / change type / add index / add entity / constraint / backfill |
| **Live records affected** | <count, or how it was established> |
| **Reversibility** | reversible / compensable / **irreversible** |
| Requirement | R<n> |
| Downtime required | |
| Approval needed | ☐ |

A count of "unknown" is acceptable only with what was tried to establish it. Proceeding on an unknown volume is
how a migration that passed locally takes production down.

## 2. Schema delta

| Entity | Field | Type | Required | Default | New / changed |
|--------|-------|------|----------|---------|---------------|
| | | | | | |

| # | Check | Result |
|---|-------|--------|
| 1 | Change is permitted by the project's schema-change policy | |
| 2 | **No field renamed in place** — add, migrate readers, remove at cutover | |
| 3 | A new required field has a default, or a backfill, or both | |
| 4 | Type changes have a compatibility window | |
| 5 | Audit fields present on any new entity | |
| 6 | Enumerated values declared where the project declares them | |
| 7 | Uniqueness enforced **in the store**, not only in application code | |
| 8 | Money and decimals not stored as binary floats | |
| 9 | Timestamps stored per the project's convention | |

Check 7: application-level uniqueness loses to concurrency. Two simultaneous requests both pass the check and
both insert.

## 3. Records that predate the change 🔴

**The section most often skipped, and the one that produces the worst bugs.**

| Question | Answer |
|----------|--------|
| How many records lack the new field? | |
| What does a read of one return today? | |
| What *should* it return? | |
| Is the value derivable from existing data? | |
| Every read path handles the absent value? | ☐ |
| Are they backfilled, defaulted at read, or left absent deliberately? | |

| Read path | Path | Handles absent value | Verified |
|-----------|------|---------------------|----------|
| | `path:line` | ☐ | |

A default declared in the schema does **not** retroactively apply to existing records in most stores. Confirm
what the store actually does rather than assuming.

## 4. Migration

| | |
|---|---|
| File | `path` |
| Direction | forward only / reversible |
| Down-migration written and tested | ☐ |
| Run by | <command> |
| Applied automatically on deploy | |
| Estimated duration at production volume | |
| Locks acquired, and for how long | |
| Zero-downtime | ☐ |

| # | Check | Result |
|---|-------|--------|
| 1 | **Expand, migrate, contract** — never contract first | |
| 2 | Tested against realistic volume, or the untested limit stated as a residual risk | |
| 3 | Lock behaviour understood — no long table lock on a live table | |
| 4 | Safe to run twice (idempotent) | |
| 5 | Safe to run concurrently with the running application | |
| 6 | Reversal path written out, not just "revert the commit" | |
| 7 | Ordering relative to the code deploy stated: schema first or code first | |

Check 7 decides whether the rollout works. Adding a required field before the code that populates it breaks
every write; deploying code that reads a field before it exists breaks every read.

## 5. Backfill

Only if data is being written or transformed.

| | |
|---|---|
| Records to process | |
| Batch size | |
| **Idempotent** | ☐ |
| **Resumable from the middle** | ☐ |
| Progress observable | |
| Rate-limited to protect live traffic | ☐ |
| Failure behaviour | |
| Verification query after completion | |

| # | Check | Result |
|---|-------|--------|
| 1 | Re-running produces the same result, not a doubled one | |
| 2 | A failure halfway does not require starting again | |
| 3 | Does not lock or saturate the store | |
| 4 | Skips records already done rather than reprocessing everything | |
| 5 | Logs progress with counts, not just start and finish | |
| 6 | A dry-run mode exists, or the first batch is inspected before the rest | |

## 6. Indexes

| Entity | Index | Fields | Supports which query | Unique |
|--------|-------|--------|---------------------|--------|
| | | | | |

| # | Check | Result |
|---|-------|--------|
| 1 | Every new query pattern has an index, or a stated reason a scan is fine at this volume | |
| 2 | Field order matches the query's usage, not alphabetical | |
| 3 | Built without blocking writes, where the store supports it | |
| 4 | No redundant index — one already covered by a prefix of another | |
| 5 | Index removals: evidence no query relies on it, from actual query patterns not assumption | |

## 7. Queries

| # | Check | Result |
|---|-------|--------|
| 1 | **Parameterised — no string interpolation** | |
| 2 | Scoped to the actor's own boundary | |
| 3 | Paginated with a cap | |
| 4 | No N+1 pattern | |
| 5 | Selects the fields needed, not everything, where it matters | |
| 6 | Uses an index — confirmed by reading the query plan, not by hoping | |
| 7 | Soft-delete respected, if the project uses it | |

## 8. Integrity

| # | Check | Result |
|---|-------|--------|
| 1 | Referential integrity enforced where the project enforces it | |
| 2 | Cascade behaviour defined and deliberate | |
| 3 | Orphan records impossible, or their handling defined | |
| 4 | State transitions validated at a single point | |
| 5 | Concurrent modification handled: optimistic version, lock, or conditional write | |
| 6 | Invariants that span records identified, and where they are enforced |  |

## 9. Sensitive data

| Field | Classification | Handling |
|-------|----------------|----------|
| | personal / secret / financial / health | encryption, masking, retention, deletion path |

| # | Check | Result |
|---|-------|--------|
| 1 | New sensitive fields classified | |
| 2 | Never logged | |
| 3 | Encrypted at rest where the store supports it | |
| 4 | Deletion path exists where retention rules require it | |
| 5 | Fixtures use placeholders, never real data | |

## 10. Verification 🔴

| # | Check | Result |
|---|-------|--------|
| 1 | Migration run forward on a copy with realistic data | |
| 2 | Migration run **backward**, if reversible | |
| 3 | Migration run **twice** — second run is a no-op | |
| 4 | A record predating the change read through every read path | |
| 5 | A new record written and read back | |
| 6 | Query plan inspected for the new query pattern | |
| 7 | Constraint violated deliberately, and rejected as expected | |
| 8 | Backfill run on a sample, verified, then the rest | |
| 9 | Fixtures and factories updated | |
| 10 | All four gates green |

| | |
|---|---|
| Realistic-volume testing done? | ☐ |
| **If not — the specific unverified condition, as a residual risk** | |

"It worked locally" on a hundred rows tells you the migration is syntactically valid. It tells you nothing about
lock duration or duration at volume, and that gap is stated rather than glossed.

## 11. Ripple effects

| Affected | Path | How | Handled by |
|----------|------|-----|-----------|
| | `path:line` | | |

Check specifically: **every read site and every write site** of the changed field · types and generated models ·
fixtures and factories · seed data · existing tests · API responses exposing the field · derived stores, caches
and reports · exports · anything filtering or sorting on it · analytics that read it directly.

**Read the write sites, not only the read sites.** A field written by a path nobody remembered is how a
"nothing uses this" claim goes wrong.

## 12. Done when

| # | Criterion |
|---|-----------|
| 1 | Schema change matches the design and the project's policy |
| 2 | Records predating the change read correctly through every path |
| 3 | Migration is idempotent, ordered correctly, with a written reversal path |
| 4 | Backfill is idempotent, batched, resumable — or none needed |
| 5 | Indexes exist for every new query pattern |
| 6 | Queries parameterised and actor-scoped |
| 7 | Verification §10 complete, unverified conditions stated |
| 8 | Ripple effects handled, write sites included |
| 9 | Nothing irreversible done without approval |
| 10 | All four gates green, no follow-up open |
