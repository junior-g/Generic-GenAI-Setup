# Session Preflight

**Load mode: ALWAYS.** This is the framework's one enforcement moment. Everything else in the rule set is an
obligation; this is the point at which compliance becomes visible.

---

## The rule

> **Before the first file edit of any session, emit the preflight block. No edit before it.**

Five lines. It costs seconds and it is the difference between a framework that is installed and a framework
that is followed.

```
PREFLIGHT
Track:     <A | B | C | T> — <slug>, or "unclassified — asking"
Stage:     <step, e.g. B4> · resume read: <artefact path, or "new work">
Gates:     <the four commands from config.yml>
Retrieval: tier <n> — <query command, or "INDEX.md + scoped search">
Pins:      <files from the feature steering file, or "none yet">
```

## Why it exists

Every rule in this framework is loaded, understood, and then not applied, for one structural reason: nothing
asks. Classification, gates, pins and the resume point are all *described* in the rule set and none of them has
a moment where the agent is stopped and made to produce them.

That gap has a measured consequence. The two most common complaints about an installed framework are "it did
not follow the feature steps" and "it never used the index". Both are the same failure: an obligation with no
checkpoint. This block is the checkpoint.

It also works *with* positional attention rather than against it — the preflight lands at the end of the
working context, immediately before the work, which is exactly where a constraint is attended to (**F10**).

## When it fires

| Situation | Preflight required |
|-----------|-------------------|
| First file edit of a session | **yes** |
| First edit after a context compaction | **yes** — the resume point must be re-read, not recalled |
| Resuming work after any break | **yes** |
| Moving to a new section (`S<n>` → `S<n+1>`) | **yes** — re-pin, and update the stage line |
| Switching to different work mid-session | **yes** — it is a new track |
| Answering a question with no edit | no |
| Reading, searching, or running a gate | no |
| Second and subsequent edits within the same section | no |

## Filling each line

### Track

The classification from [`80-work-intake.md`](80-work-intake.md). **The user states it.** If you do not have
it, the correct value is `unclassified — asking`, and that is a **stop**: ask, and do not edit.

You may propose a different track with a reason. You may not pick one silently — that is how a bug fix
acquires a design stage, and how an enhancement skips its regression sweep.

`T` is a complete answer for a trivial change. It is void the moment the change touches a type, a schema, a
role, a response shape, or routing configuration.

### Stage and resume read

Which step of the track you are at, and **which artefact you read to establish that**.

The resume read is not optional on a resume. Naming a file you did not open is **F5**; reconstructing the state
from memory is **F9**. On new work, `new work` is the honest value.

### Gates

The four concrete commands, from `config.yml` → `gates`. Copied, not recalled.

Writing them out at the start is what makes running them at the end automatic. It also surfaces a broken
install immediately: if you cannot fill this line, `config.yml` was never completed, and that is a finding
before you have written any code.

### Retrieval

The tier and its command, from `config.yml` → `retrieval`. At tiers 0 and 1 the honest value is
`INDEX.md + scoped search` — but say it, because naming the tier is what stops the reflex to read directories
wide (**F4**).

If the project has no `INDEX.md` content, the install is incomplete. Say so and run the install audit
([`../setup/install-audit.md`](../setup/install-audit.md)) rather than working around it.

### Pins

The working set from the feature steering file `<rules_root>/8x-<slug>.md`. `none yet` before `A2`/`B2`.

If a pin has never been read, drop it. If the list has grown past a handful, it is not a working set (**F4**).

## What makes it a stop, not a formality

Three lines can each halt the work:

| Line | Halts when | Because |
|------|-----------|---------|
| **Track** | it reads `unclassified` | proceeding means improvising a process, which is the failure the tracks exist to prevent |
| **Gates** | you cannot fill it from `config.yml` | there is no definition of done, so nothing can be completed |
| **Retrieval** | the project has no index content | you are about to read directories wide |

A preflight that cannot be completed is more useful than one that is completed dishonestly. Report the gap.

## Worked examples

**Resuming an enhancement:**

```
PREFLIGHT
Track:     B — customer-archive-state
Stage:     B9, section S2 · resume read: .ai/work/customer-archive-state/tasks/execution-report-S1.md
Gates:     npm run lint · npm run typecheck · npm run build · npm test
Retrieval: tier 0 — INDEX.md + scoped search over src/
Pins:      src/models/customer.ts · src/api/customers/list.ts · .ai/work/customer-archive-state/do-not-break.md
```

**Trivial change:**

```
PREFLIGHT
Track:     T — correcting the empty-state copy on the orders list
Stage:     n/a · resume read: new work
Gates:     npm run lint · npm run typecheck · npm run build · npm test
Retrieval: tier 0 — file named by user
Pins:      none
```

**A stop:**

```
PREFLIGHT
Track:     unclassified — asking
Stage:     — · resume read: new work
Gates:     ./gradlew ktlintCheck · ./gradlew compileKotlin · ./gradlew assemble · ./gradlew test
Retrieval: tier 1 — IDE semantic search
Pins:      none

Adding an export endpoint adds a route and a response shape, so this is Track B, not T.
Confirm the track and I will fill the enhancement raw-requirement template. Not editing yet.
```

## What it is not

- **Not a status report.** It is five lines, before the work, not a summary after it.
- **Not a substitute for the track.** It states which process applies; the track file *is* the process.
- **Not repeated per file.** Once per session, and again at each section boundary.
- **Not ceremony to be abbreviated away.** The line most tempting to drop is Track, and Track is the one that
  matters most.

## Failure modes this closes

| Code | Without the preflight |
|------|----------------------|
| **F2** | the objective is never restated, so drift has nothing to correct against |
| **F9** | the resume point is recalled instead of read, and finished work is redone or contradicted |
| **F10** | constraints given early are no longer in view at the moment of editing |
| **F4** | retrieval tier unstated, so the reflex is to read directories wide |
| **F12** | no stage means no budget checkpoint |

And the meta-failure that produced this rule: a framework fully installed and not followed, because no moment
ever required it. Diagnostic for that:
[`../knowledge/ai_troubleshooting.md`](../knowledge/ai_troubleshooting.md) §9.
