# IDE Matrix

Known instruction mechanisms per tool, and which of the four load modes each supports.

> **Treat every row as a hypothesis, not an answer.** These conventions change, and a stale row is exactly the
> trap activation step `S2` exists to catch. Verify against your own documentation or an existing file in the
> repository before relying on a row — and **if you find a row wrong, fix it here** as part of the install.
> That is a contribution, not a deviation.

Load modes are defined in [`../manifest.md`](../manifest.md) §1. Content contract:
[`README.md`](README.md).

---

## The universal fallback

**`AGENTS.md` at the repository root.** It is an open, tool-agnostic convention, and most current coding
assistants read it without configuration. When you cannot establish your own mechanism with confidence, use it
and say plainly that you fell back to the open standard.

That is a correct outcome, not a failure. Template: [`AGENTS.template.md`](AGENTS.template.md).

## Matrix

| Tool | Mechanism | ALWAYS | AUTO | ON-DEMAND | FILE-MATCH | Notes |
|------|-----------|:------:|:----:|:---------:|:----------:|-------|
| **Any / unknown** | `AGENTS.md` at root | ✅ | ➖ | ➖ | ➖ | The fallback. Degrade AUTO and ON-DEMAND to a named-and-described list |
| **Kiro** | `.kiro/steering/*.md` with front matter | ✅ | ✅ | ✅ | ✅ | `inclusion: always` / `auto` / `manual` / `fileMatch` + `fileMatchPattern`. Closest native fit to the manifest |
| **Cursor** | `.cursor/rules/*.mdc` | ✅ | ➕ | ✅ | ✅ | Per-rule front matter with `globs` and an always-apply flag. Legacy `.cursorrules` still read by older versions |
| **Claude Code** | `CLAUDE.md`, nested per directory | ✅ | ➖ | ✅ | ➕ | Nested files act as a coarse FILE-MATCH — a directory-level file loads with that directory's work |
| **GitHub Copilot** | `.github/copilot-instructions.md` | ✅ | ➖ | ➖ | ➕ | Also supports scoped `*.instructions.md` files with `applyTo` globs in recent versions — verify for your version |
| **Windsurf** | `.windsurf/rules/*.md` | ✅ | ➕ | ✅ | ➕ | Activation modes per rule; legacy `.windsurfrules` |
| **Cline / Roo** | `.clinerules` (file or directory) | ✅ | ➖ | ✅ | ➖ | A directory lets you keep one file per rule, which maps well onto the rule set |
| **Aider** | `CONVENTIONS.md`, added as a read-only file | ✅ | ➖ | ✅ | ➖ | Files are added to the chat explicitly, which makes ON-DEMAND natural and ALWAYS manual |
| **Continue** | `.continue/rules/` or config rules | ✅ | ➕ | ✅ | ➕ | Glob support varies by version — verify |
| **Zed** | `.rules` at root | ✅ | ➖ | ➖ | ➖ | Also reads several common alternatives including `AGENTS.md` |
| **Gemini CLI** | `GEMINI.md` | ✅ | ➖ | ➖ | ➖ | Hierarchical, similar to nested `CLAUDE.md` |
| **JetBrains AI / Junie** | `.junie/guidelines.md` | ✅ | ➖ | ➖ | ➖ | Verify the current path for your IDE version |
| **Qwen Code** | `QWEN.md` | ✅ | ➖ | ➖ | ➖ | Follows the same pattern as other single-file conventions |
| **Plain LLM chat, no file access** | attach [`../../MAIN.md`](../../MAIN.md) | ➕ | ➖ | ➖ | ➖ | Operates on the eleven non-negotiables. Do not reconstruct missing rules from memory (**F11**) |

**Legend:** ✅ native · ➕ partial, verify for your version · ➖ not supported, degrade per
[`README.md`](README.md#degrading-a-mode-safely)

## How to read the matrix

| Column | Means |
|--------|-------|
| ALWAYS | the mechanism can put a file in context on every request |
| AUTO | it can load a file based on a description of the request |
| ON-DEMAND | a file can be pulled in by name, by the user or the agent |
| FILE-MATCH | a file loads when a matching path is opened or edited, usually via globs |

**Only ALWAYS must be reproduced properly.** Everything else degrades safely to "named and described in the
ALWAYS adapter", because an agent that knows a file exists and what it covers will open it when relevant.

## Verifying a row

Three methods, in order of strength:

| # | Method | Strength |
|---|--------|----------|
| 1 | Your own system context or published documentation names the path | strong — say *how* you know |
| 2 | The file or directory already exists in this repository | strong — and it tells you what this team's assistants read |
| 3 | This matrix | **weak.** A starting hypothesis only |

If methods 1 and 2 both fail, use `AGENTS.md`.

```bash
ls -a
ls -a .github .cursor .windsurf .continue .kiro .zed .junie 2>/dev/null
find . -maxdepth 2 \( -iname 'AGENTS.md' -o -iname 'CLAUDE.md' -o -iname 'GEMINI.md' \
  -o -iname 'QWEN.md' -o -iname '.cursorrules' -o -iname '.windsurfrules' \
  -o -iname 'CONVENTIONS.md' -o -iname 'copilot-instructions.md' -o -iname '.clinerules' \
  -o -iname '.rules' \) 2>/dev/null
```

## When a tool supports globs

Use them. A native glob reproduces FILE-MATCH properly instead of degrading it, which means
`15-design-system.md` loads when someone edits a component rather than when someone remembers to ask for it.

Globs come from `config.yml` → `paths.file_match`:

| Rule | Glob source |
|------|-------------|
| `project/15-design-system.md` | `paths.file_match.ui` |
| `project/20-api-standards.md` | `paths.file_match.api` |
| `project/30-data-and-types.md` | `paths.file_match.data` |

## When several tools share one repository

Normal. Multiple adapters over one rule set is the design.

| # | Rule |
|---|------|
| 1 | One rule set in `.ai/rules/`. Adapters reference it; none owns it |
| 2 | **Never delete another tool's adapter** — irreversible, and not yours to decide (**F6**) |
| 3 | One [`adapters-log.md`](adapters-log.md) entry per adapter |
| 4 | Two adapters instructing differently is **F11** — stop and ask which wins |
| 5 | Prefer extending `AGENTS.md` over adding a fifth near-duplicate file |

## Adding a tool

1. Establish the mechanism by evidence, not assumption. Record which method established it.
2. Determine each load mode: supported, partial, or not — and what partial means concretely.
3. Note the trap. Legacy path still honoured? Glob syntax that differs from the obvious? A size limit? That
   line is usually the most valuable part of the row.
4. Add the row, with the legend symbols used consistently.
5. Log the install in [`adapters-log.md`](adapters-log.md), including what degraded and how you compensated.
