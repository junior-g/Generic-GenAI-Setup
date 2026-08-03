# Stack Profiles — mapping the four gate roles onto real commands

The framework defines four gates as **roles**, never as commands:

| Role | Question it answers |
|------|---------------------|
| **LINT** | Does the code meet the house style, with zero errors *and* zero warnings? |
| **STATIC** | Does it pass type checking, or the strongest static analysis the language offers? |
| **BUILD** | Does it compile / bundle / package cleanly? |
| **TEST** | Does the full suite pass in a single terminating run? |

Every rule in the framework refers to the role. Only `.ai/config.yml` knows the command. That indirection
is the whole of the framework's language independence.

> **These profiles are starting points, not answers.** The authoritative source for a project's gate
> commands is its own manifest and CI configuration. Read those, run each command, and record what
> actually happened — see [`discovery-checklist.md`](discovery-checklist.md) §1.2. A profile row you never
> executed is a guess (**F5**).

---

## Three rules that apply to every profile

1. **Zero warnings.** Warnings that are permanently tolerated stop being read. If the project has a
   warning backlog it cannot clear today, record the count in `config.yml` as a baseline and require that
   it does not grow — but say that is what you did.
2. **No watch modes.** A gate must terminate and return an exit code. Find the flag that forces a single
   run and put it in `gates.test.single_run_flag`.
3. **A missing gate is declared, not deleted.** If a language has no type checker, `gates.static` gets the
   strongest available static analysis and `substituted: true`. Four gates always exist; sometimes one of
   them is standing in for another.

---

## `javascript-typescript`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `npm run lint` → ESLint / Biome / oxlint | Add `--max-warnings 0` if the script does not already fail on warnings |
| STATIC | `npm run typecheck` → `tsc --noEmit` | Plain JS with no `tsconfig`: substitute `eslint` with type-aware rules, or JSDoc-checked `tsc`, and set `substituted: true` |
| BUILD | `npm run build` | Framework-specific. Never assume; read the script |
| TEST | `npm test` → Vitest / Jest / node:test | Vitest and Jest default to watch in some setups: `vitest run`, `jest --watchAll=false` |

Swap `npm` for `pnpm`, `yarn` or `bun` based on the **lockfile**, not preference. Monorepos: the gate is
the workspace-wide command (`turbo run lint`, `nx run-many`, `pnpm -r`), not the command in one package.

## `python`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `ruff check .` · `flake8` · `pylint <pkg>` | Formatting is separate: `ruff format --check .` or `black --check .`. Include both in the lint gate |
| STATIC | `mypy .` · `pyright` · `ty check` | If the project has no annotations, `substituted: true` with `ruff` + `bandit`, and say so |
| BUILD | `python -m build` · `poetry build` · `uv build` | A pure application with no packaging: substitute an import-smoke check (`python -c "import <pkg>"`) or the container build |
| TEST | `pytest -q` · `python -m unittest discover` | `-p no:cacheprovider` in CI-like runs. Never `-f`/`--looponfail` |

Read `pyproject.toml` in full — tool config sections there decide the flags. Respect the environment
manager the project uses (`poetry run`, `uv run`, `hatch run`, activated venv); running a bare `pytest`
against the wrong interpreter produces failures that are about you, not the code.

## `java-kotlin`

| Role | Gradle | Maven |
|------|--------|-------|
| LINT | `./gradlew ktlintCheck spotlessCheck checkstyleMain` | `mvn spotless:check checkstyle:check` |
| STATIC | `./gradlew compileJava compileKotlin` (+ `detekt`, `spotbugsMain`) | `mvn compile` (+ `spotbugs:check`) |
| BUILD | `./gradlew assemble` | `mvn package -DskipTests` |
| TEST | `./gradlew test` | `mvn test` |

The compiler *is* the static gate here; add SpotBugs/Detekt on top where configured. Always use the
wrapper (`./gradlew`, `./mvnw`) — a locally installed version is a different toolchain. `./gradlew build`
runs check and test together; keep the roles separate so a failure names itself.

## `go`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `golangci-lint run` | Fall back to `gofmt -l .` + `go vet ./...` if not configured. `gofmt -l` printing anything is a failure |
| STATIC | `go vet ./...` | The compiler covers typing; `vet` is the real static gate. `staticcheck ./...` if present |
| BUILD | `go build ./...` | |
| TEST | `go test ./...` | `-race` where the project uses concurrency; `-count=1` to defeat the result cache |

## `rust`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `cargo clippy --all-targets -- -D warnings` | Plus `cargo fmt --check`. `-D warnings` is how zero-warning is enforced |
| STATIC | `cargo check --all-targets` | The type system is the gate |
| BUILD | `cargo build --release` | Debug build is acceptable if release is prohibitively slow — record the choice |
| TEST | `cargo test` | `cargo nextest run` if the project uses it |

## `dotnet-csharp`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `dotnet format --verify-no-changes` | Analyzer warnings surface in build; `TreatWarningsAsErrors` is the real enforcement |
| STATIC | `dotnet build /warnaserror` | Roslyn analyzers run in build |
| BUILD | `dotnet build -c Release` | |
| TEST | `dotnet test` | |

Because build and static overlap, note in `config.yml` that they share a command rather than duplicating
it silently.

## `php`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `vendor/bin/php-cs-fixer fix --dry-run --diff` · `vendor/bin/phpcs` | |
| STATIC | `vendor/bin/phpstan analyse` · `vendor/bin/psalm` | Record the configured level; raising it is a separate task |
| BUILD | `composer validate --strict` + `composer install --dry-run` | Interpreted, so this gate is dependency and autoload integrity |
| TEST | `vendor/bin/phpunit` · `vendor/bin/pest` | |

## `ruby`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `bundle exec rubocop` | |
| STATIC | `bundle exec srb tc` (Sorbet) · `steep check` (RBS) | Neither present: `substituted: true` with `rubocop` + `bundle exec ruby -c` syntax check |
| BUILD | `gem build *.gemspec` · `bundle exec rake assets:precompile` | Rails: asset precompile is the meaningful build |
| TEST | `bundle exec rspec` · `bundle exec rake test` | |

## `swift`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `swiftlint --strict` · `swift-format lint -r .` | |
| STATIC | `swift build` · `xcodebuild -scheme <s> build` | Compiler-enforced |
| BUILD | `swift build -c release` · `xcodebuild archive` | |
| TEST | `swift test` · `xcodebuild test -scheme <s> -destination <d>` | |

## `c-cpp`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `clang-format --dry-run --Werror <files>` · `clang-tidy` | |
| STATIC | `cppcheck --error-exitcode=1 .` · compiler `-Wall -Wextra -Werror` | Sanitiser builds belong here too where configured |
| BUILD | `cmake --build build` · `make` · `meson compile -C build` | |
| TEST | `ctest --output-on-failure` · `make test` | |

## `elixir`

| Role | Typical command | Notes |
|------|-----------------|-------|
| LINT | `mix format --check-formatted` + `mix credo --strict` | |
| STATIC | `mix dialyzer` | First run builds a PLT and is slow; cache it and note the cost |
| BUILD | `mix compile --warnings-as-errors` | |
| TEST | `mix test` | |

## Cross-cutting layers

These are not profiles; they are extra gates that attach to whatever profile the project uses. Each goes
in `gates.extra` with the condition that makes it required.

| Layer | Gate | Condition |
|-------|------|-----------|
| Containers | `docker build .` · `hadolint Dockerfile` | Dockerfile or compose file changed |
| Infrastructure-as-code | `terraform validate` + `terraform fmt -check` · `tflint`; `pulumi preview` | IaC files changed. **`plan`/`preview` only — `apply` is irreversible and needs approval (F6)** |
| Kubernetes | `kubeval` · `kustomize build` · `helm lint` | Manifests or charts changed |
| SQL / migrations | migration dry-run, `sqlfluff lint` | Schema or migration files changed |
| OpenAPI / schema | `spectral lint` · `openapi-generator validate` · `buf lint` | Contract files changed |
| Security | `npm audit` · `pip-audit` · `cargo audit` · `trivy fs .` | Dependencies changed |
| Docs / links | Markdown link check | Documentation changed |
| Accessibility | `axe` / `pa11y` in an automated run | UI changed and the project has an a11y bar |

---

## Adding a profile

Nothing here is exhaustive. To add one:

1. Read the ecosystem's manifest format and find where scripts/tasks are declared.
2. Identify the canonical linter, static analyser, build command and test runner **as that community
   actually uses them**, not as they could theoretically be assembled.
3. Find the flag that forces a single terminating test run.
4. Decide what stands in when a role genuinely has no tool, and note that it is a substitution.
5. Add the table above, in the same shape.
6. Note the trap that catches newcomers — the version-manager mismatch, the watch-mode default, the
   wrapper-vs-global toolchain confusion. That line is usually the most valuable part of the row.

## Recording the outcome

Whatever you conclude ends up in `.ai/config.yml`'s `gates` block, and the *evidence* ends up in the
activation report:

```yaml
gates:
  lint:
    command: "npm run lint"
    zero_warnings: true
    notes: "eslint 9 flat config; script already passes --max-warnings 0. Verified: 0 errors, 0 warnings."
  static:
    command: "npm run typecheck"
    substituted: false
    notes: "tsc --noEmit, strict true. Verified clean."
```

A `notes` field saying "verified clean" when you ran the command is worth more than any table in this
file. A `notes` field left empty means the next session has to rediscover what you already knew.
