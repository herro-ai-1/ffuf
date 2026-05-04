# Project structure

Overview of this repository layout for contributors and coding agents.

## Root

| Path | Role |
|------|------|
| `main.go`, `help.go` | CLI entrypoints |
| `go.mod`, `go.sum` | Go module definition |
| `README.md`, `CHANGELOG.md`, `LICENSE` | Project metadata |
| `claude.md` | Shared AI agent conventions (from team gist) |
| `CONTRIBUTING.md` | Contribution notes including `agent_activity.log` format |
| `dev_best_practices.md` | Development and PR practices (gist) |
| `task_replan_planner.md` | Task replan algorithm reference (gist) |
| `agentic_workflow_helper.md` | Agentic workflow reference (gist) |
| `health_check_endpoint.py` | Local health probe script (gist; writes `health.log`) |
| `docs/PRD_PLANNING.md` | Product requirements planning kickoff and PRD skeleton |

## Packages (`pkg/`)

| Directory | Role |
|-----------|------|
| `pkg/ffuf/` | Core fuzzing engine, config, HTTP client plumbing |
| `pkg/input/` | Wordlists and input handling |
| `pkg/output/` | Result formats (stdout, JSON, CSV, etc.) |
| `pkg/filter/` | Response filters |
| `pkg/runner/` | Job execution |
| `pkg/scraper/` | HTML scraping helpers |
| `pkg/interactive/` | Terminal interaction |

## Tests

Go tests live alongside code as `*_test.go` files under `pkg/` and other packages. Run:

```bash
go test ./...
```

There is no top-level `tests/` directory; pytest hooks in `claude.md` are optional no-ops unless Python tests are added later.

## CI

GitHub Actions workflows live under `.github/workflows/` (e.g. CodeQL, golangci-lint).

## Local-only files (never commit)

- `health.log` — health probe output
- `agent_activity.log` — agent JSONL activity log
