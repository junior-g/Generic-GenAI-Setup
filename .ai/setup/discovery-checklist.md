# Discovery Checklist

The evidence-gathering commands for activation steps `S1` (discover the project) and `S2` (discover your
own instruction mechanism), and what each finding decides.

Protocol: [`activation-protocol.md`](activation-protocol.md). Gate mappings:
[`stack-profiles.md`](stack-profiles.md).

> Every command here is **read-only**. Nothing in discovery writes, installs, or mutates. If you find
> yourself wanting to run an installer to find something out, stop — the answer is in a file.

---

## §1 — Project discovery

### 1.1 What language and toolchain

Look for manifests before looking at file extensions. A repository full of `.ts` files might be built by
four different toolchains, and the manifest is the only thing that says which.

```bash
ls -a
ls -a | grep -iE 'package.json|pyproject.toml|setup.py|requirements|Gemfile|go.mod|Cargo.toml|pom.xml|build.gradle|.*\.csproj|.*\.sln|composer.json|mix.exs|Package.swift|CMakeLists.txt|Makefile|deno.json|build.zig'
```

Then read the manifest you found. Not skim — read the scripts/tasks block in full, because that is where
the gate commands are.

Lockfiles disambiguate the package manager when the manifest does not:

```bash
ls -a | grep -iE 'package-lock.json|pnpm-lock.yaml|yarn.lock|bun.lockb|poetry.lock|uv.lock|Pipfile.lock|Gemfile.lock|go.sum|Cargo.lock|composer.lock|packages.lock.json'
```

| Found | Decides |
|-------|---------|
| the manifest | `project.languages`, `project.stack_profile` |
| the lockfile | `project.package_manager` — and never run a different one |
| the scripts/tasks block | all four `gates.*.command` values |
| version pin files (`.nvmrc`, `.python-version`, `.tool-versions`, `.sdkmanrc`) | `project.runtime_versions` |

### 1.2 The four gates — find them, then run them

```bash
# whatever the manifest's script/task listing command is, e.g.:
npm run            # node
poetry run python -c "pass" && cat pyproject.toml   # python
./gradlew tasks --all | head -50                    # gradle
go help            # go
cargo --list       # rust
make -qp | grep -E '^[a-zA-Z]' | head -30           # make-driven
```

Cross-check against CI, which frequently runs gates the manifest does not expose:

```bash
ls -a .github/workflows .gitlab-ci.yml .circleci Jenkinsfile azure-pipelines.yml .buildkite 2>/dev/null
```

Then **run each one**. Timeboxed, in this order — cheapest first:

| Order | Gate | Why this order |
|-------|------|----------------|
| 1 | lint | fastest, and its config tells you the house style |
| 2 | static / type | second fastest, and reveals whether the codebase actually type-checks clean today |
| 3 | test | before build, because a failing suite is a more important finding |
| 4 | build | slowest; run last and separately |

Record for each: the exact command, whether it passed, and the tail of the output. A gate that fails on a
clean checkout goes in `config.yml`'s `unresolved` block and is reported — it is a finding about the
project, and fixing it is not part of activation.

**Watch modes are not gates.** Find the flag that forces a single terminating run and put it in
`gates.test.single_run_flag`. A gate that never exits cannot be a gate.

### 1.3 The tree, and where things live

```bash
# depth-limited; a full recursive listing is a context-overflow trap (F4)
find . -maxdepth 2 -type d -not -path '*/.git*' -not -path '*/node_modules*' \
  -not -path '*/.venv*' -not -path '*/target*' -not -path '*/build*' | sort
```

Then go one level deeper only into the directories that look like source, test, UI, API and data layers.

| Looking for | Typical signals |
|-------------|-----------------|
| source root | the manifest's declared entry point or package dir |
| test root | the test runner's configured path, not a guess |
| UI layer | component/view/template/screen directories, a stylesheet system, a component library dependency |
| API layer | route/controller/handler/resolver directories, a server framework dependency |
| data layer | model/entity/schema/migration directories, an ORM or driver dependency |
| constants | a config/constants module imported widely |
| env access | a settings/env module that validates, or raw environment reads scattered — note which |

Absence is a finding too. A project with no UI layer does not get a design-system rule, and recording
that decision is what stops a later session generating one.

### 1.4 Documentation and existing conventions

```bash
ls -a | grep -iE 'readme|contributing|architecture|docs?$|adr|changelog'
find . -maxdepth 3 -iname '*.md' -not -path '*/node_modules*' -not -path '*/.git*' | head -40
```

| Found | Decides |
|-------|---------|
| a docs directory | `paths.docs_root`, and Zone 2 rows in `INDEX.md` |
| `CONTRIBUTING.md` | conventions that are already agreed — do not contradict them, cite them |
| ADRs | decisions already made; a design that reverses one needs to say so |
| a changelog | the project's release discipline |
| nothing at all | tier 0 retrieval is likely right, and Zone 2 starts as documentation debt |

Read `README.md` and `CONTRIBUTING.md` properly. Then treat their claims as claims: a README that says
"run `make test`" against a repo with no Makefile is documentation debt, and finding that at `S1` is
worth more than any rule you will write later.

### 1.5 Constants, secrets and environment

```bash
ls -a | grep -iE '\.env|secrets|\.envrc'
```

Read `.env.example` or its equivalent for the **key names only**. Never read or echo real secret values —
`.env`, `.env.local`, credential stores and keystores are off limits unless a task genuinely requires one,
and even then values are referenced by key name.

| Finding | Decides |
|---------|---------|
| a validated settings/env module | `paths.env_module` — feature code reads env only through it |
| raw environment reads scattered through features | a row in `70-do-not-regress.md` and a convention to fix it forward |
| a single constants module | `paths.constants_module` |
| brand/domain strings hardcoded in several places | a project convention to centralise, recorded in `10-code-conventions.md` |

---

## §2 — Your own instruction mechanism

### 2.1 Check yourself first

Your own system context or published documentation usually states the convention. If it does, say *how*
you know — "my system prompt names this path" is evidence; "this is the usual convention" is not.

### 2.2 Look for what already exists

```bash
ls -a
ls -a .github .cursor .windsurf .continue .kiro .zed .clinerules .aider* 2>/dev/null
find . -maxdepth 2 \( -iname 'AGENTS.md' -o -iname 'CLAUDE.md' -o -iname 'GEMINI.md' \
  -o -iname '.cursorrules' -o -iname '.windsurfrules' -o -iname 'CONVENTIONS.md' \
  -o -iname 'copilot-instructions.md' -o -iname '.clinerules' -o -iname 'QWEN.md' \) 2>/dev/null
```

An existing file is strong evidence of what this repository's assistants read. It is also something you
must not delete (**F6**) — add alongside.

### 2.3 Consult the matrix, then correct it

[`../adapters/ide-matrix.md`](../adapters/ide-matrix.md) lists known mechanisms per IDE and the load modes
each supports. It is a **hypothesis**. Verify before relying on a row, and if you find a row wrong, fix it
in the matrix as part of the install.

### 2.4 Record

| Field | Value |
|-------|-------|
| Assistant / IDE | |
| Mechanism (exact paths) | |
| How confirmed | own docs / found in repo / fell back to `AGENTS.md` |
| ALWAYS supported? | |
| AUTO supported? | fallback if not |
| ON-DEMAND supported? | fallback if not |
| FILE-MATCH supported? | fallback if not |
| Front matter / glob syntax available? | |

Cannot determine it? Use `AGENTS.md`, say so plainly, and move on. That is the designed fallback, not a
failure.

---

## §3 — Discovery output

`S1` and `S2` produce one message, not a document. It states:

1. Language, toolchain, package manager, with the file each came from.
2. The four gate commands, and the real result of running each.
3. Source / test / UI / API / data roots, and any layer that does not exist.
4. Docs present, and any documentation debt spotted.
5. Constants and env access pattern.
6. Your instruction mechanism and which load modes you support.
7. Anything you could not establish, and what you checked.

Item 7 being non-empty is normal. Item 7 being empty on a codebase you have known for ten minutes is a
sign you inferred something you should have read.
