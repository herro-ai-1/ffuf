# Contributing

Thank you for contributing to ffuf. For pull requests, use `.github/pull_request_template.md`: describe the change, link issues, and update `CHANGELOG.md` when behavior or user-visible output changes.

## Agent activity log (`agent_activity.log`)

This file is optional and **gitignored**. Agents may append **JSON lines** (JSONL) for cost and workflow review. Suggested fields:

| Field | Description |
|-------|-------------|
| `ts` | ISO 8601 timestamp (UTC) |
| `agent_id` | Stable identifier for the agent/session |
| `tool` | Tool or command category invoked |
| `tokens_in` | Input tokens if available |
| `tokens_out` | Output tokens if available |
| `summary` | Short human-readable description of the action |

Example line:

```json
{"ts":"2026-05-04T12:00:00Z","agent_id":"cursor-session","tool":"read_file","tokens_in":0,"tokens_out":0,"summary":"Reviewed pkg/ffuf/config.go"}
```

Human contributors can ignore this file.

## Code style (Go)

Run before submitting:

```bash
gofmt -w <files-you-changed>
go vet ./...
go test ./...
```

Additional conventions are summarized in `claude.md` (including local git hook examples).
