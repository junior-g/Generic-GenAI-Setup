<!--
TEMPLATE — preserved-contract register. Copy to <artefact_root>/<slug>/do-not-break.md, delete this header.
Written at step B1. MANDATORY on Track B, including B-lite. Filled by the AGENT.

THIS IS THE ONLY REAL CONTROL AGAINST THE UNKNOWN-CONSUMER FAILURE. The two most expensive wrong claims in
this framework's history were both "removing this is impact-free". Both were scoped-search negatives. Both
survived two review stages.

FILL IT BY READING CONSUMERS, NOT SEARCHING FOR THEM. A search finds the consumers that use the name you
searched for. It does not find the one that reaches the data another way.

THE REGISTER IS NOT FROZEN AFTER B1. The B6 regression sweep appends to it. So does anything discovered
during execution.

Sections are not deleted. Write None or Not applicable.
-->

# Do Not Break — <slug>

| | |
|---|---|
| Written | <date> |
| Last appended | <date, and at which step> |
| Rows | <n> |
| Rows verified green | <n> |
| Deliberately broken | <n> |

Every row is verified before the work is called done. A row with no check in the **Proof** column is not a
contract, it is a hope.

---

## 1. Routes and screens

| Route / screen | Path | Who links to it | State it expects | Proof it still holds |
|----------------|------|-----------------|------------------|---------------------|
| | `path:line` | `path:line` (read ✅) | | <the check> |

## 2. API and interface responses

One row per **field** a consumer reads, not per endpoint. A response's shape is a contract at field
granularity.

| Endpoint | Field | Type | Read by | Proof |
|----------|-------|------|---------|-------|
| | | | `path:line` (read ✅) | |

## 3. Events, messages, and payloads

| Event / topic | Payload field | Subscriber | Proof |
|---------------|---------------|-----------|-------|
| | | `path:line` (read ✅) | |

## 4. Schema fields

| Entity | Field | Readers | Writers | Records predating it | Proof |
|--------|-------|---------|---------|---------------------|-------|
| | | `path:line` | `path:line` | <count / unknown> | |

**Read the write sites, not only the read sites.** A field that is written by a path nobody remembered is how
a "nothing uses this" claim goes wrong.

## 5. Permissions and entry points

| Actor | Reaches what | Via which entry point | Proof |
|-------|-------------|----------------------|-------|
| | | <menu, hub, shared role list, route guard> | |

An actor whose permission survives but whose entry point disappears is stranded — the capability exists and
is unreachable. Both columns matter.

## 6. Localised strings and content keys

| Key | Read by | Locales present | Proof |
|-----|---------|-----------------|-------|
| | `path:line` | | |

## 7. Shared components, styles, and utilities

| Thing | Usage sites | What they depend on it for | Proof |
|-------|------------|---------------------------|-------|
| | `path:line` (read ✅) | | |

Include the **loading** dependency, not just the reference: a class referenced on a surface that never loads
the stylesheet renders unstyled and passes every test.

## 8. Public exports and integration points

| Export | Consumed by | Inside or outside this repo | Proof |
|--------|-------------|----------------------------|-------|
| | | | |

## 9. Existing tests asserting behaviour that is changing

| Test | Asserts | Still valid after this change? | Proof |
|------|---------|-------------------------------|-------|
| `path:line` | | yes / must change | |

**A test that must change is a contract change**, justified here in advance. Editing a test during
implementation to make the suite pass is the single most common way a contract breaks silently.

| Test that must change | Why the contract is changing | Approved by user? |
|----------------------|------------------------------|-------------------|
| | | ☐ |

## 10. Behavioural invariants

Contracts with no single code location — an ordering, a default, a timing, an idempotency guarantee.

| Invariant | Why it matters | How it is checked |
|-----------|----------------|-------------------|
| | | |

These are the hardest to find because there is nothing to search for. Ask the user what depends on the
current behaviour; treat the answer as a lead, then verify it.

---

## 11. Contracts being deliberately broken

| # | Contract | Why | Migration path | Reversal path | User approved |
|---|----------|-----|----------------|---------------|---------------|
| 1 | | | | | ☐ |

A row here without a ticked approval box blocks the work.

## 12. Verification summary

Completed at the regression section of execution. Every row above appears here.

| Row | Contract | Check run | Result | Where evidenced |
|-----|----------|-----------|--------|-----------------|
| 1.1 | | | ✅ / ❌ | `execution-report-S<n>.md` |

**Every row green is a condition of done.** A row that cannot be verified locally is stated as a residual
risk with the specific unverified condition — not marked green.

## 13. Appended after `B1`

Contracts discovered later. The register grows; it is never closed early.

| Date | Step | Contract | Found how |
|------|------|----------|-----------|
| | `B6` / `B9` | | |
