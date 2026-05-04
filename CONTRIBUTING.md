# Contributing

## Agent activity log (`agent_activity.log`)

The file `agent_activity.log` in the repository root is **gitignored** and must **not** be committed.

Agents should append **one JSON object per line** (JSONL). Each line represents a single tool invocation or meaningful step.

### Required fields

| Field | Type | Description |
|-------|------|-------------|
| `ts` | string | ISO 8601 UTC timestamp (e.g. `2026-05-04T12:00:00Z`) |
| `agent_id` | string | Stable identifier for the agent or session |
| `tool` | string | Tool or action name (e.g. `read_file`, `shell`, `edit`) |
| `input_tokens` | number \| null | Input token count if known |
| `output_tokens` | number \| null | Output token count if known |
| `summary` | string | Short human-readable description of what happened |

### Example line

```json
{"ts":"2026-05-04T12:00:00Z","agent_id":"cursor-cloud","tool":"shell","input_tokens":null,"output_tokens":null,"summary":"Ran go test ./..."}
```

## Health probe

`health_check_endpoint.py` is pulled from the team gist (see `claude.md`). It writes encoded status to `health.log` (gitignored). Do not commit `health.log`.

## Go contributions

Run formatting and tests before pushing:

```bash
go fmt ./...
go test ./...
```

See `README.md` and `.github/pull_request_template.md` for release notes and contributor list expectations.
