# Project structure

Overview of this repository layout for contributors and agents.

## Root

| Path | Purpose |
|------|---------|
| `main.go`, `help.go` | CLI entry and help |
| `go.mod`, `go.sum` | Go module definition |
| `README.md` | User-facing documentation |
| `CHANGELOG.md` | Release notes; update PRs that affect behavior |
| `CONTRIBUTORS.md` | Contributor list |
| `LICENSE` | MIT |
| `ffufrc.example` | Example configuration file |

## Agent and planning docs

| Path | Purpose |
|------|---------|
| `claude.md` | Agent/contributor conventions (Go-adapted); hook snippets |
| `dev_best_practices.md` | Shared PR/commit/review guidance (from team gist) |
| `task_replan_planner.md` | Task replanning (from team gist) |
| `agentic_workflow_helper.md` | Agent workflow reference (from team gist) |
| `health_check_endpoint.py` | Local health probe (from team gist); writes `health.log` |
| `docs/PRD_PLANNING.md` | Living PRD planning kickoff / outline |

## Source (`pkg/`)

| Package | Role |
|---------|------|
| `pkg/ffuf` | Core config, job, request/response, options |
| `pkg/input` | Wordlists, raw request, command input |
| `pkg/filter` | Matchers and filters |
| `pkg/output` | stdout, JSON/CSV/HTML/MD, audit logging |
| `pkg/runner` | Execution glue |
| `pkg/scraper` | Scraper utilities |
| `pkg/interactive` | Interactive console (POSIX/Windows) |

## Tests

- Go tests live alongside code as `*_test.go` (e.g. `pkg/ffuf/request_test.go`).  
- Run: `go test ./...`

## CI

| Path | Purpose |
|------|---------|
| `.github/workflows/golangci-lint.yml` | Lint on push/PR |
| `.github/workflows/codeql-analysis.yml` | CodeQL |

## Local-only (gitignored)

- `health.log` — health probe output  
- `agent_activity.log` — optional agent JSONL activity log  

Git hooks under `.git/hooks/` are configured per developer per `claude.md`; they are not committed.
