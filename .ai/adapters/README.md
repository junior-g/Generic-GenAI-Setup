# Adapters

An adapter is the thin layer that makes **this IDE** load the rule set in the modes
[`../manifest.md`](../manifest.md) specifies. Written at activation step `S6`.

The rules themselves are plain Markdown with no IDE-specific syntax — any assistant can read them directly.
What differs between tools is only *when* each file loads, and that is all an adapter encodes.

| File | Purpose |
|------|---------|
| [`ide-matrix.md`](ide-matrix.md) | known instruction mechanisms per tool, and which load modes each supports |
| [`AGENTS.template.md`](AGENTS.template.md) | the universal fallback, and the content contract every adapter satisfies |
| [`adapters-log.md`](adapters-log.md) | one entry per install: what was set up, what degraded, drift hashes |

---

## The one hard rule: reference, never copy

> An adapter tells the agent **which files to read**. It does not restate their content.

Copying rule content into an adapter creates two sources for one rule. They drift — always, and usually within
weeks — and then two sessions behave differently on the same question with no way to tell which is right. That
is **F11**, and it is the failure this framework spends the most structure preventing.

**The one sanctioned exception** is the eleven non-negotiables in [`../../MAIN.md`](../../MAIN.md). They are
inlined deliberately: short, stable, and missing one is more expensive than the drift risk.

## If your mechanism genuinely cannot follow a pointer

Some tools only read a single file and will not open referenced ones. Then the adapter is *generated*, and the
install must record in [`adapters-log.md`](adapters-log.md):

- which files were inlined, and the date
- a content hash of each source at generation time
- the command to re-check drift

```bash
# macOS
md5 -q .ai/rules/05-failure-detection.md
# Linux
md5sum .ai/rules/05-failure-detection.md
# any platform with Python
python3 -c "import hashlib,sys;print(hashlib.md5(open(sys.argv[1],'rb').read()).hexdigest())" .ai/rules/05-failure-detection.md
```

A changed hash means the adapter is stale — regenerate it. That is the mechanical guard, and it is the
difference between a known-lagging copy and an unknown one.

## What every adapter must contain

Ten items. The filename and front matter change per tool; the content contract does not.

| # | Item | From |
|---|------|------|
| 1 | What this project is — one paragraph, never invented | `rules/project/product.md` |
| 2 | The ALWAYS list, as paths | [`../manifest.md`](../manifest.md) §2 |
| 3 | How work starts — the A/B/C/T table | `rules/80-work-intake.md` |
| 4 | The four gate commands, concretely | `config.yml` → `gates` |
| 5 | The grounding rule — a pointer is never evidence; negatives need two checks | `rules/00-agent-contract.md` §1 |
| 6 | Hard stops — irreversible actions ask first; removals go to cutover | `rules/00-agent-contract.md` |
| 7 | Retrieval: the project's tier and its commands | `config.yml` → `retrieval` |
| 8 | The `F1`–`F13` vocabulary exists; where the table and playbooks are | `rules/05-failure-detection.md` |
| 9 | ON-DEMAND and FILE-MATCH files, listed with purpose and glob | [`../manifest.md`](../manifest.md) §2 |
| 10 | Auto-healing and 3× verification — one line each, with paths | `rules/65`, `rules/62` |

**Roughly one page.** An adapter that restates the whole rule set has recreated the drift problem it exists to
avoid.

Items 9 and 10 are the ones most often dropped, and dropping them is what causes a silent capability loss: an
agent that has never heard of a file will not open it, however relevant it is.

## Writing one

1. **Establish the mechanism by evidence** — activation `S2`. Your own documentation, an existing file in the
   repository, or [`ide-matrix.md`](ide-matrix.md) treated as a hypothesis and then verified.
2. **Map the four load modes** to what the tool supports, and note the fallback for each gap.
3. **Use native front matter and globs if they exist.** They reproduce FILE-MATCH properly rather than
   degrading it to ON-DEMAND.
4. **Write the ten items**, as references.
5. **Prove the twelve behaviour checks** — activation `S8`. Files existing is not an install.
6. **Log it** in [`adapters-log.md`](adapters-log.md), including what degraded.

## Degrading a mode safely

| Mode | If unsupported |
|------|----------------|
| ALWAYS | every tool supports some form of this. If yours truly does not, say so — the framework cannot function |
| AUTO | put a three-line summary plus the path in the ALWAYS adapter, so the agent knows to open it |
| ON-DEMAND | list the path and a one-line purpose in the ALWAYS adapter, so it is discoverable |
| FILE-MATCH | downgrade to ON-DEMAND and list the glob beside it, so the agent can self-trigger |

Everything except ALWAYS degrades safely to "named and described". A known file gets opened when relevant; an
unknown one never does.

## Multiple assistants on one repository

Common, and fine. Several adapters can coexist over one rule set — that is the point of the split.

| # | Rule |
|---|------|
| 1 | One rule set. Adapters reference it; none of them owns it |
| 2 | Never delete or rewrite another tool's adapter. It is irreversible and not yours to decide (**F6**) |
| 3 | Each adapter gets its own [`adapters-log.md`](adapters-log.md) entry |
| 4 | If two adapters instruct differently, that is **F11** — stop and ask which wins |
| 5 | `AGENTS.md` is the shared fallback most assistants read natively. Prefer extending it over adding a fifth file |

## Contributing back

Setting up a tool not in [`ide-matrix.md`](ide-matrix.md), or finding a row wrong, means the matrix is wrong.
Fix it as part of the install — that is a contribution, not a deviation. Include how you established the
mechanism, so the next reader knows whether to trust the row.
