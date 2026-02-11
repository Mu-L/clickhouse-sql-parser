# Repository Guidelines

## Project Structure & Module Organization
- `main.go` is the CLI entry point (`clickhouse-sql-parser`) for AST output and SQL formatting.
- `parser/` contains core parser code: lexer (`lexer.go`), AST definitions (`ast.go`), traversal helpers (`walk.go`), and grammar-specific parser files (`parser_query.go`, `parser_table.go`, `parser_alter.go`, etc.).
- Tests live next to source as `*_test.go` files, with fixtures under `parser/testdata/`.
- Fixture groups are organized by SQL type (`basic/`, `query/`, `dml/`, `ddl/`), with generated expectations in `output/` (AST JSON) and `format/` (formatted SQL).

## Build, Test, and Development Commands
- `make` (or `go build -o clickhouse-sql-parser main.go`): build the CLI binary.
- `make test`: run full test suite with race detection, coverage output (`coverage.out`), and compatibility tests.
- `make update_test`: regenerate golden fixtures after intentional parser/formatter output changes.
- `make lint`: run `golangci-lint` using the repo configuration.
- `go test -bench=. -benchmem ./parser`: run parser benchmarks.

## Coding Style & Naming Conventions
- Use Go 1.21 conventions (`go.mod`) and keep code `gofmt`/`goimports` clean (enforced by lint).
- Place parsing logic in the matching module by statement family (for example, query parsing in `parser/parser_query.go`).
- Follow existing parser naming patterns such as `parseXxx` helpers and explicit AST type names.
- Keep AST `String()` output deterministic; formatting changes must be reflected in golden files.

## Testing Guidelines
- Use Go’s `testing` package with `testify/require` assertions and `goldie` snapshot comparisons.
- Add new SQL cases as `.sql` files under the appropriate `parser/testdata/<category>/` directory.
- If expected outputs change, run `make update_test` and commit updated files in both `output/` and/or `format/`.
- Prefer descriptive test names (`TestParser_*`, `TestWalk_*`) and subtests for per-fixture coverage.

## Commit & Pull Request Guidelines
- Match existing commit style: concise, imperative subjects like `Add support for ...` or `Fix parsing failure ...`, optionally with issue refs (for example `(#235)`).
- Keep PRs focused; describe grammar/AST impact, include representative SQL examples, and note regenerated fixtures.
- Before requesting review, run `make lint` and `make test` locally to mirror CI expectations.
