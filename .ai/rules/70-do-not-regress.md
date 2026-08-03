# Do Not Regress

**Load mode: ALWAYS.** A regression is prevented at the moment of writing, or not at all.

This file has two parts. **Part 1 is universal** and ships with the framework. **Part 2 is the project's own
scar tissue** — appended at activation step `S5` and grown every time a defect is fixed.

If a change seems to require one of these, **stop and flag it** rather than regressing. There is sometimes a
legitimate reason; there is never a silent one.

---

## Part 1 — Universal

Patterns that are defects in any language, on any stack.

| Don't | Do instead | Why |
|-------|-----------|-----|
| Make a gate pass by weakening it — widening a type, adding a suppression, deleting an assertion, excluding a file | Fix the cause. If the gate is genuinely wrong, change it as a separate, stated decision | Converts a caught failure into an uncaught one |
| Use the language's escape-hatch "any" type to get past a static error | Read the real declaration and use it (**F13**) | The error was the only thing that knew about the mismatch |
| Interpolate a value into a query, shell command, or file path | Parameterise, or use the escaping API | Injection |
| Read raw environment variables in feature code | Go through the project's validated accessor | A missing value should fail at startup, not at 3am |
| Hardcode a brand string, endpoint, limit, or magic constant in a component | Import from the project's declared constants module | Duplicated constants diverge silently |
| Log a full request body, token, or credential | Log identifiers and outcomes | Secrets end up in aggregators and stay there |
| Catch an exception and continue with a default that hides it | Handle it, or let it propagate. Never `catch {}` | A swallowed error is a silent data corruption |
| Derive state in an effect and write it back to state | Compute it where it is used | Extra renders, stale reads, loops |
| Retry an operation that already had an external effect, without an idempotency key | Confirm what the first call did before repeating it (**F6**) | Duplicate charges, duplicate mail, burned sequence numbers |
| Edit a test so the suite goes green | A test that must change is a **contract change** — justify it in advance | The test was the only record of the contract |
| Commit a lockfile-less dependency change, or an open version range | Pin, and commit the lockfile | Non-reproducible builds |
| Add a network-exposed surface with no auth and not mention it | Add auth, or state the gap explicitly | Silence makes it the next person's surprise |
| Delete or move a document to "clean up" during feature work | Removals go to a separately approved cutover step | Irreversible, and something linked to it |
| Leave a `TODO`, stub, or fake return value in delivered work | Finish it, or record it as a named open question | A placeholder reads like a decision |
| Treat a scoped search miss as proof something doesn't exist | Read the file. Negatives need two checks by different methods | The two most expensive wrong claims in this framework's history |
| Mix a removal or retirement into a feature section | Defer it to the separately approved cutover step | Feature work and removal have different approval requirements |
| Resume from memory after a break or compaction | Read the status table and the latest execution report first | **F9** — duplicated and contradictory work |
| Edit the framework's universal rules (`rules/00`–`95`) in place | Report the issue upstream; keep project content in `rules/project/` | Breaks the re-sync guarantee |

## Part 2 — Project-specific

> Generated at activation step `S5` and appended to for the life of the project. Empty in the pristine
> framework copy.

Add a row when:

- A bug fix reveals a **class** of mistake, not a one-off — Track C step `C7`.
- A pattern was deliberately retired and something might reintroduce it.
- A framework or library version in use makes a once-correct pattern wrong.
- A convention exists that a reasonable person would violate by default.

| Don't | Do instead | Added | Source |
|-------|-----------|-------|--------|
| _(one row per fenced pattern)_ | | | |

### Row format

Each row carries four things, and a row missing any of them will not be followed:

| Field | Requirement |
|-------|-------------|
| **Don't** | The specific pattern, concretely enough to recognise. "Don't write bad queries" fences nothing |
| **Do instead** | The replacement. A prohibition with no alternative gets worked around |
| **Added** | Date, so a row can be re-examined when the reason expires |
| **Source** | The fix report, execution report, or upgrade note that produced it — the evidence |

### Retiring a row

Rows are not immortal. A row whose reason has expired — the version was upgraded, the retired module is
gone, the pattern is now impossible — should be removed, with the reason recorded in the commit. A register
full of dead rows stops being read, which is the failure mode this file exists to prevent.

Retiring a row is a deliberate, stated act. It is not a cleanup you do in passing.
