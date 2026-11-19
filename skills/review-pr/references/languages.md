# Language notes

Defaults for steps 7 (checks) and 8 (repro). Prefer the project's own commands (`package.json` scripts, `Makefile`, CI config) when they exist; use these only to fill gaps.

The SKILL.md **trust boundary** applies to every row: reuse shared dependencies (symlinked `node_modules`, the main virtualenv, `vendor/`, `vendor/bundle`) only for a trusted head; otherwise use an isolated copy or don't run. Write build output to `$SCRATCH`, never into the main checkout.

| Language | Dependencies in the worktree | Type-check / build | Lint (changed files) | Targeted tests | Repro as |
|---|---|---|---|---|---|
| JS / TS | symlink `node_modules` from the main checkout | `tsc --noEmit` | `eslint <files>` | `jest <file>` / `vitest run <file>` | script at the worktree root, run with `tsx` / `ts-node` |
| Python | use the main checkout's virtualenv interpreter | `mypy <pkg>` / `pyright` | `ruff check <files>` | `pytest <file> -k <name>` | script at the worktree root run with `<venv>/bin/python` (not `-I`, which drops the worktree from `sys.path`), or a temporary pytest test |
| Go | shared module cache, nothing to link | `go build ./... && go vet ./...` | `golangci-lint run <pkgs>` | `go test ./<pkg> -run <Name>` | temporary `zz_repro_test.go` in the package |
| Java | shared `~/.m2` / Gradle cache; build offline | `mvn -q -o compile` / `gradle compileJava --offline` | Checkstyle / SpotBugs if configured | `mvn -o -Dtest=<Class>#<method> test` / `gradle test --tests <Class>` | temporary JUnit test |
| .NET | shared `~/.nuget` cache | `dotnet build --no-restore` | `dotnet format --verify-no-changes --include <files>` | `dotnet test --no-build --filter <Name>` | temporary xUnit / NUnit test |
| Rust | `CARGO_TARGET_DIR="$SCRATCH/target"` (copy `<repo>/target` there first to save build time) | `cargo check` | `cargo clippy` | `cargo test <name>` | temporary `#[test]` |
| Ruby | `vendor/bundle` from the main checkout (`BUNDLE_PATH`) | — | `rubocop <files>` | `rspec <file>:<line>` | script run with `bundle exec ruby` |
| PHP | `vendor/` from the main checkout | `phpstan analyse <files>` | `phpcs <files>` | `phpunit --filter <name>` | script run with `php` |

## Notes

- **Compiled languages (Go, Java, .NET, Rust):** write the repro as a temporary test in the same package, so it can reach unexported code and uses the real build. Run it with the test filter above, and delete it afterwards.
- **Slow builds:** a full Java, .NET or Rust build in a fresh worktree can take minutes. If it's too slow for the review, build only the changed module or project, or write "build not run: too slow" in the scope note.
- **Private package registries** (Maven, NuGet, npm, PyPI with auth): offline or cached builds work only if the main checkout has already restored the packages. If not, write "couldn't run: private registry" in the scope note; don't try to log in.
- **Native modules that fail to load** in a repro and aren't related to the finding (e.g. a native `bcrypt` addon): stub only that module (Node: a `--require` preload that intercepts it), and say so in the report.
