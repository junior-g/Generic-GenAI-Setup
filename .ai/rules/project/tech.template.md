<!--
TEMPLATE — copy to tech.md, delete this header block, fill every section.
Generated at activation step S5. Load mode: ALWAYS.

Purpose: the stack, the four gate commands, and the version-specific traps. This is the file that stops the
agent writing a pattern that was correct two major versions ago.

Evidence rule: versions come from the manifest and lockfile, never from memory of "what's current". Gate
commands are the ones you RAN at S1, with the real result recorded. A command in this file that has never
been executed is a guess (F5), and the first real gate run will expose it.

The gate commands here must match config.yml exactly. If they ever disagree, that is F11 — config.yml wins,
because it is the machine-readable source.

Keep it under ~130 lines.
-->

# Tech — <project name>

## Stack

| Layer | Choice | Version | Pinned in |
|-------|--------|---------|-----------|
| Language | | | `path` |
| Runtime | | | `path` |
| Framework | | | `path` |
| Package manager | | | lockfile at `path` |
| Data store | | | |
| Test runner | | | |
| Linter / formatter | | | |
| Static analysis | | | |
| Build tool | | | |
| CI | | | `path` |
| Deploy target | | | |

## The four gates — the definition of done

All four pass before anything is reported complete. Real output, never a claim.

```bash
<lint command>        # zero errors AND zero warnings
<static command>      # zero errors
<build command>       # succeeds
<test command>        # full suite green, single terminating run
```

| Gate | Verified at setup | Result | Notes |
|------|-------------------|--------|-------|
| lint | <date> | <pass / n findings> | |
| static | <date> | | <"substituted" if this is a stand-in> |
| build | <date> | | |
| test | <date> | | <n tests> |

### Additional gates

| Gate | Command | Required when |
|------|---------|---------------|
| | | |

### Gate notes

- <How to force a single non-watch test run.>
- <Anything slow enough to need running separately.>
- <Any pre-existing failure and its cause — recorded, not silently absorbed.>

## Version-specific rules — do not use older patterns

**The highest-value section in this file.** Every row is a pattern that was correct in an earlier version and
is wrong now. Without this the agent writes plausible, deprecated code that passes review because it looks
familiar.

| Don't | Do instead | Since | Source |
|-------|-----------|-------|--------|
| | | version | `path:line` or release note |

## Configuration and environment

| | |
|---|---|
| Constants | `path` — import; never hardcode a brand string, endpoint, or limit |
| Validated environment accessor | `path` — the only place raw environment is read |
| Required environment keys | listed in `path` (names only, never values) |
| Feature flags | `path`, or "none" |
| Local setup command | `<command>` |

## Local development

```bash
<install>
<run>
<test>
```

| | |
|---|---|
| Long-running processes | Start in the background, never as a blocking call. Poll for readiness |
| Known local-only breakage | <e.g. a dev-server bug that forces validating against a production build — state the workaround> |
| Ports / services needed | |
| Seed / fixture data | |

## Dependency policy

- Prefer what is already here. Every new dependency is justified in the design, naming what it replaces.
- Pin exact or narrowly bounded versions. Commit the lockfile.
- Verify the package name character by character before adding it.
- Run `<audit command>` after any dependency change.

| Deliberately avoided | Why | Use instead |
|---------------------|-----|-------------|
| | | |

## Performance and limits

Only what is actually a constraint. An invented budget is worse than none.

| Concern | Budget | Measured? | Where |
|---------|--------|-----------|-------|
| | | yes / no | |

## Observability

| | |
|---|---|
| Logging | <library, level convention, what must never be logged> |
| Metrics | |
| Tracing | |
| Error reporting | |

## Where to read more

| Topic | Document |
|-------|----------|
| Architecture | |
| Deployment | |
| Data model | |
