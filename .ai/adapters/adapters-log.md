# Adapters Log

One entry per IDE set up against this rule set. Written at activation step `S8`, after the twelve behaviour
checks have been run.

**Why it exists:** an install that claims success without evidence is the exact failure the whole activation
protocol is designed to prevent. This log is where the evidence lives — including the honest gaps.

> **A recorded 10/12 with named gaps is a good entry. A claimed 12/12 without probes is not** (**F8**, **F9**).

| Where to go | For |
|-------------|-----|
| [`README.md`](README.md) | how to write an adapter, and the reference-not-copy rule |
| [`ide-matrix.md`](ide-matrix.md) | mechanisms per tool and their load-mode support |
| [`../manifest.md`](../manifest.md) §3 | the twelve behaviour checks |
| [`../setup/activation-protocol.md`](../setup/activation-protocol.md) | the `S0`–`S8` runbook |

---

## Entries

_None yet — this is the pristine framework copy. In an installation there is one entry per adapter._

---

## Entry template

Copy this block, fill it, append it above. Do not overwrite an existing entry: a re-sync gets a **new** entry
that references the old one, so the history of what degraded stays visible.

### `<Tool name and version>` — <date>

| | |
|---|---|
| Project | |
| Framework version installed | |
| Assistant / model | |
| Mechanism used | `<exact paths created>` |
| **How the mechanism was established** | own documentation / found existing in repo / **fell back to `AGENTS.md`** |
| Adapter files created | |
| Existing instruction sets found and **left alone** | |

#### Load-mode mapping

| Manifest mode | Native? | What was done |
|---------------|---------|---------------|
| ALWAYS | ✅ / ➕ / ➖ | |
| AUTO | | |
| ON-DEMAND | | |
| FILE-MATCH | | |

#### Project layer generated

| File | Generated | Omitted, and why |
|------|-----------|------------------|
| `product.md` | ☐ | |
| `structure.md` | ☐ | |
| `tech.md` | ☐ | |
| `10-code-conventions.md` | ☐ | |
| `15-design-system.md` | ☐ | |
| `20-api-standards.md` | ☐ | |
| `30-data-and-types.md` | ☐ | |
| `90-docs-map.md` | ☐ | |

#### Gates resolved

| Gate | Command | Verified | Result |
|------|---------|----------|--------|
| lint | | ☐ | |
| static | | ☐ | <note "substituted" if it stands in for a real type check> |
| build | | ☐ | |
| test | | ☐ | |

| | |
|---|---|
| Pre-existing failures found, recorded not fixed | |
| Gates that could not be run, and why | |

#### Retrieval

| | |
|---|---|
| Tier chosen | |
| Rationale | |
| Build and one real query run | ☐ |

#### Behaviour checks — the actual result

| # | Behaviour | Probe used | Result |
|---|-----------|-----------|--------|
| 1 | Work classified before starting | | ☐ |
| 2 | Pointer is not evidence | | ☐ |
| 3 | Four gates define done | | ☐ |
| 4 | Round caps are hard | | ☐ |
| 5 | Irreversible actions stop and ask | | ☐ |
| 6 | Failure signals named by code | | ☐ |
| 7 | Retrieval before directory sweeps | | ☐ |
| 8 | Every step has a template | | ☐ |
| 9 | Artefacts have a declared home | | ☐ |
| 10 | Do-not-regress register binds | | ☐ |
| 11 | Documentation self-heals | | ☐ |
| 12 | Nothing completes on one look | | ☐ |

| | |
|---|---|
| **Score** | **<n>/12** |
| Checks that failed, and why | |
| Adapter fixes attempted | |
| **Known gaps carried forward** | |

#### Content inlined

Only if the mechanism genuinely cannot follow a pointer. Empty is the expected and preferred state.

| File inlined | Hash at generation | Date |
|--------------|-------------------|------|
| | | |

| | |
|---|---|
| Drift check command | `<the hash command for this platform>` |
| Re-check when | before relying on the adapter after any framework re-sync |

A changed hash means the adapter is stale — regenerate it. This is the mechanical guard against **F11**.

#### What degraded, and the compensation

| Capability | Degraded how | Compensated by |
|------------|-------------|----------------|
| | | |

#### Anything that could not be confirmed

| Unknown | What was checked | Default applied |
|---------|-----------------|-----------------|
| | | |

A non-empty table here is honest. An empty one on a codebase seen for ten minutes suggests something was
inferred that should have been read (**F5**).

---

## Re-sync entries

A re-sync is a **new entry**, not an edit to the old one.

| Field | Note |
|-------|------|
| Previous framework version | |
| New framework version | |
| Universal rules refreshed | `rules/00`–`95`, templates, manifest, retrieval spec — safe to overwrite |
| **Never overwritten** | `rules/project/**`, `config.yml`, `INDEX.md` Zone 2, `work/**` |
| Differences reported to the user | |
| Behaviour checks re-run | ☐ — **required**, since an upgrade can change what they expect |
| New score | <n>/12 |

## Reading this log

| Pattern across entries | What it means |
|-----------------------|---------------|
| The same check failing on several tools | the check's probe may be unclear, or the manifest's expectation unrealistic — worth revisiting upstream |
| One tool consistently scoring low | its mechanism needs a better adapter, not a lower bar |
| Content inlined repeatedly for one tool | consider whether that tool can be made to follow pointers at all, and note it in [`ide-matrix.md`](ide-matrix.md) |
| Gates recorded as unverified | the install was incomplete; the first real gate run will expose it |
