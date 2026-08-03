<!--
TEMPLATE — copy to 90-docs-map.md, delete this header block, fill every section.
Generated at activation step S5. Load mode: ON-DEMAND.

Generate this if the project has documentation of its own. If it has none, skip it and record the gap as
documentation debt in .ai/INDEX.md Zone 2 — an empty map is worse than an honest absence.

This is the CURATED READING ORDER. It is not a file listing — .ai/INDEX.md already lists everything. The
value here is sequencing: what to read first to understand something, and what is safe to skip.

Keep it under ~110 lines.
-->

# Docs Map — <project name>

The curated path through the project's own documentation. Complete inventory:
[`../../INDEX.md`](../../INDEX.md). This file answers a different question — **in what order**, and **what
can be skipped**.

| | |
|---|---|
| Docs root | `path` |
| Total documents | <n> |
| Last verified current | <date> |

---

## Reading paths by question

### "I am new here and need the shape of the system"

| # | Read | Why | Time |
|---|------|-----|------|
| 1 | | | |
| 2 | | | |
| 3 | | | |

Stop after this. Anything further is depth you do not need yet.

### "I need to change a user-facing surface"

| # | Read |
|---|------|
| 1 | |

### "I need to change or add a boundary / endpoint"

| # | Read |
|---|------|
| 1 | |

### "I need to change the data model"

| # | Read |
|---|------|
| 1 | |

### "Something is broken in production"

| # | Read |
|---|------|
| 1 | |

### "I need to deploy or change infrastructure"

| # | Read |
|---|------|
| 1 | |

## Full inventory, annotated

| Document | Covers | Status | Skip if |
|----------|--------|--------|---------|
| | | current / stale / partial | |

The **Skip if** column is the one that saves the most: knowing a document is irrelevant to your task is worth
as much as knowing it is essential.

## Decision records

<Where architectural decisions are recorded, if anywhere. A design that reverses a recorded decision must
say so explicitly rather than quietly contradicting it.>

| ADR | Decision | Status |
|-----|----------|--------|
| | | accepted / superseded by <n> |

## Documents that are authoritative

When two documents disagree, these win. Everything else is descriptive and may be stale.

| Topic | Authoritative source | Why |
|-------|---------------------|-----|
| Gate commands | `.ai/config.yml` | machine-readable, verified at setup |
| Conventions | `10-code-conventions.md` | the rule layer |
| The code's actual behaviour | **the code** | documentation describes; code is |

**Code beats documentation, always.** When they disagree, the document is wrong until proven otherwise — heal
it ([`../65-auto-healing-docs.md`](../65-auto-healing-docs.md)). The exception: if the code contradicts an
explicit requirement, the code is the defect. Raise it; do not document the bug as intended behaviour.

## Known documentation debt

Mirrors the debt table in [`../../INDEX.md`](../../INDEX.md). Keep them in sync — two divergent debt lists is
**F11**.

| Gap | Severity | Raised by | Plan |
|-----|----------|-----------|------|
| | | | |

## Generated documentation

| Document | Generated from | Regenerate with |
|----------|----------------|-----------------|
| | | |

Do not hand-edit these. Change the generator.
