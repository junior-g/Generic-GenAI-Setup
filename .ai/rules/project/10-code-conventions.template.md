<!--
TEMPLATE — copy to 10-code-conventions.md, delete this header block, fill every section.
Generated at activation step S5. Load mode: ALWAYS.

Purpose: so generated code is indistinguishable from code the team wrote. Style consistency is not
cosmetic — inconsistent code makes review slower and hides real defects among noise.

Evidence rule: a convention needs THREE call sites. One occurrence is a coincidence, two is a pattern, three
is a convention. Where the codebase disagrees with itself, say so and name the preferred direction rather
than inventing a resolution.

Do not restate what the linter already enforces. If the formatter handles it, the rule is "run the
formatter". This file is for what tooling cannot check.

Keep it under ~140 lines.
-->

# Code Conventions — <project name>

Enforced, not aspirational. <If a past cleanup removed N findings caused by drifting from these, say so —
that number is the reason the file exists.>

**Anything the linter or formatter enforces is not repeated here.** Run `<lint command>`. This file covers
what tooling cannot check.

## Imports and module references

| Rule | Example | Evidence |
|------|---------|----------|
| <e.g. absolute alias imports, not deep relative paths> | `<good>` vs `<avoid>` | `path:line` |

| | |
|---|---|
| Import order | <groups and their order, or "formatter-enforced"> |
| Aliases | <the alias map and where it is configured> |
| Circular dependencies | <how they are detected, and what to do instead> |
| Barrel / index files | <used or avoided, and why> |

## Typing and contracts

| Rule | Why | Evidence |
|------|-----|----------|
| <e.g. no escape-hatch type; read the declaration instead> | a widened type converts a caught failure into an uncaught one (**F13**) | `path:line` |

| | |
|---|---|
| Where types live | `path` |
| Shared vs local types | <the boundary> |
| Validation at boundaries | <the library and the pattern> |
| Nullability convention | |

## Error handling

| Rule | Example |
|------|---------|
| <the project's error type or hierarchy> | `path:line` |
| <when to catch vs propagate> | |
| <the error envelope returned to callers> | |

Never swallow an error into a default that hides it. A `catch` with an empty body is a silent data-corruption
bug.

## Async and concurrency

| | |
|---|---|
| Convention | <async/await, futures, channels, promises — the one the project uses> |
| Cancellation / timeouts | |
| Parallelism | <how, and the bound on it> |
| Unhandled rejection / panic policy | |

## Logging

| | |
|---|---|
| Logger | `path` |
| Levels and when to use each | |
| Structured fields expected | |
| **Never logged** | secrets, tokens, full request bodies, personal data |

## Comments and documentation in code

| | |
|---|---|
| Doc comments required on | <public API / exported symbols / nothing> |
| Format | |
| Inline comments | explain **why**, never restate what the code does |
| Commented-out code | deleted, not left. Version control is the archive |

## Function and module shape

| | |
|---|---|
| Preferred size | <a real observed number, not a wish> |
| Pure-logic extraction | Extract logic so it is testable without a store, a network, or a UI |
| Parameter count / object params | |
| Return conventions | |

## Tests

| | |
|---|---|
| Location | <co-located or a test root — `path`> |
| Naming | |
| Structure | <arrange-act-assert or the project's shape> |
| Fixtures / factories | `path` |
| Mocking policy | <what may be mocked, and what must be real> |
| What must always be tested | |

## Forbidden patterns

The ones a reasonable person would reach for by default. Each row is a real trap in this codebase.

| Don't | Do instead | Why |
|-------|-----------|-----|
| | | |

For patterns that have already caused a defect, the row belongs in
[`../70-do-not-regress.md`](../70-do-not-regress.md) Part 2 instead — that file is the register of things
that have actually bitten.

## Formatting

<One line. Which formatter, and that it is authoritative. Do not restate its rules.>

## Where the codebase is inconsistent

Honesty about drift, so an agent does not mistake old code for the convention.

| Topic | Pattern A | Pattern B | Preferred going forward | Migrated? |
|-------|-----------|-----------|-------------------------|-----------|
| | | | | no |
