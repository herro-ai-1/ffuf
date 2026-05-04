## Development Environment

Make sure your local environment is set up correctly before starting work.

```bash
# Clone and install
git clone <repo-url> && cd know_your_boundaries
pip install -r requirements.txt
cp .env.example .env

# Verify setup
python3 -m pytest tests/ -q
```

If you're using an AI coding agent (Codex, Claude Code, Cursor, etc.), ensure it has
access to the project root and can run shell commands within the workspace.

## Branch Strategy

We follow a trunk-based model with short-lived feature branches.

- Branch from `main` for all work
- Name branches: `feat/short-description`, `fix/short-description`, or `chore/short-description`
- Rebase onto `main` before opening your MR — do not merge main into your branch
- Delete branches after merge

## Writing Good Commits

Each commit should represent a single logical change. Write messages in imperative mood
with a concise subject line (50 chars max) and an optional body explaining *why*, not *what*.

```
feat: add rate limiting to /api/chat endpoint

The endpoint was vulnerable to rapid-fire requests that could exhaust
the LLM token budget. This adds a sliding window limiter (60 req/min
per session) with a 429 response and Retry-After header.
```

Avoid commits like "fix stuff", "WIP", or "address review comments" in the final history.
Squash or fixup before merge if needed.

## Pull Request Guidelines

### Size

Keep PRs small and focused. A good PR changes fewer than 300 lines across no more than
5-6 files. If your change is larger, break it into a stack of dependent PRs.

Why this matters: large PRs get rubber-stamped. Small PRs get real reviews.

### PR Description Template

Every MR should include:

```markdown
## What

One-sentence summary of the change.

## Why

Context: what problem does this solve? Link to issue/ticket if applicable.

## How

Brief explanation of the approach. Call out any non-obvious decisions.

## Testing

- How you verified this works (manual steps, new tests, etc.)
- Edge cases considered

## Checklist

- [ ] Tests pass locally (`pytest tests/ -q`)
- [ ] No new lint warnings (`ruff check .`)
- [ ] Documentation updated if needed
- [ ] Reviewed my own diff before requesting review
```

### Reviewing Your Own Diff

Before requesting review, always check your own diff:

```bash
git diff main...HEAD --stat        # overview of what changed
git diff main...HEAD               # full diff
git diff main...HEAD -- '*.py'     # just python changes
```

Read it as if you're the reviewer. Look for: leftover debug prints, commented-out code,
unintended file changes, secrets or tokens accidentally committed.

### Resolving Conflicts

If `main` has moved ahead, rebase:

```bash
git fetch origin
git rebase origin/main

# If conflicts arise:
# 1. Fix conflicts in each file
# 2. git add <resolved-files>
# 3. git rebase --continue
# Do NOT use git merge to resolve — keep history linear.
```

## Code Style

### Python

We use `ruff` for linting and formatting. Configuration lives in `pyproject.toml`.
Run before every commit:

```bash
ruff check . --fix
ruff format .
```

### Comments

Write comments that explain *why*, not *what*. The code shows what; comments
should provide context that isn't obvious from reading the implementation.

```python
# Bad:
# Increment counter by one
counter += 1

# Good:
# Rate limiter requires at least one warm-up request before
# enforcing the sliding window, so we pre-seed the counter.
counter += 1
```

For complex logic, add a brief block comment above the section:

```python
# The LLM sometimes returns tool calls with trailing whitespace in
# argument values, which breaks JSON parsing downstream. We strip
# here rather than in the parser to keep the parser input-agnostic.
cleaned = {k: v.strip() if isinstance(v, str) else v for k, v in args.items()}
```

### Docstrings

Use Google-style docstrings for public functions:

```python
def resolve_tool_call(call: ToolCall, registry: ToolRegistry) -> ToolResult:
    """Execute a tool call against the registry and return the result.

    Args:
        call: The parsed tool call from the LLM response.
        registry: Available tools for the current session.

    Returns:
        ToolResult with status, output, and token usage metadata.

    Raises:
        ToolNotFoundError: If the tool name isn't in the registry.
    """
```

## Agent Activity Logging

When using AI coding agents in this project, all agent activity must be logged
to `agent_activity.log` in the project root. This file is gitignored and stays local.

### What to Log

Every interaction with the agent should produce a log entry containing:

- **Timestamp** (ISO 8601)
- **Agent identifier** (e.g., `codex-cli/0.92`, `claude-code/1.x`, `cursor/0.4x`)
- **Action type**: `tool_call`, `file_edit`, `shell_command`, `llm_query`
- **Tool name** (if applicable): the specific tool or command invoked
- **Token usage**: input tokens, output tokens, total (if available from the provider)
- **Model**: which model handled the request (e.g., `gpt-5.1-codex-mini`, `claude-opus-4-6`)
- **Duration**: wall-clock time for the operation
- **Summary**: one-line description of what the action did

### Log Format

Use JSONL (one JSON object per line) for easy parsing:

```jsonl
{"ts":"2026-02-02T14:30:01Z","agent":"codex-cli/0.92","action":"shell_command","tool":"shell_command","cmd":"pytest tests/ -q","tokens":{"in":1240,"out":85,"total":1325},"model":"gpt-5.1-codex-mini","duration_ms":3200,"summary":"Ran test suite, 42 passed"}
{"ts":"2026-02-02T14:30:15Z","agent":"codex-cli/0.92","action":"file_edit","tool":"apply_patch","file":"src/limiter.py","tokens":{"in":2100,"out":340,"total":2440},"model":"gpt-5.1-codex-mini","duration_ms":4100,"summary":"Added sliding window rate limiter"}
{"ts":"2026-02-02T14:31:02Z","agent":"codex-cli/0.92","action":"tool_call","tool":"web_fetch","url":"https://...","tokens":{"in":800,"out":1200,"total":2000},"model":"gpt-5.1-codex-mini","duration_ms":2800,"summary":"Fetched API reference for rate limit headers"}
```

### Setup

The logging helper is included in `scripts/`:

```bash
# Initialize the log file (safe to re-run)
touch agent_activity.log

# Tail during a session to monitor agent behavior
tail -f agent_activity.log | python3 -m json.tool --no-ensure-ascii
```

If your agent supports hooks or callbacks, configure it to append to this file.
For agents without native logging, wrap commands with the log helper:

```bash
# Example: log a tool call manually
echo '{"ts":"'$(date -u +%Y-%m-%dT%H:%M:%SZ)'","agent":"manual","action":"shell_command","cmd":"ruff check .","summary":"Lint check"}' >> agent_activity.log
```

### Why We Log

Agent activity logs help us understand:
- Token spend per task (cost tracking)
- Which tools agents rely on most (workflow optimization)
- Unexpected tool calls or file access patterns (security review)
- Reproducibility — if an agent made a change, the log shows how

This is especially important in a security-focused project. Reviewing `agent_activity.log`
after each session is part of our security hygiene.

## CI / Pre-Merge Checks

Our CI pipeline runs on every MR:

1. `ruff check .` — lint
2. `ruff format --check .` — formatting
3. `pytest tests/ -q` — unit tests
4. `bandit -r src/` — security scan

All four must pass before merge. Fix locally rather than pushing to see if CI passes.

## Post-Merge Verification

After your MR is merged to `main`, verify the deployment is healthy:

```bash
git checkout main && git pull origin main
```

## Helpful Resources

- [Python Style Guide](https://google.github.io/styleguide/pyguide.html) — our baseline
- [Conventional Commits](https://www.conventionalcommits.org/) — commit message spec
- [Ruff Documentation](https://docs.astral.sh/ruff/) — linter/formatter config
- [Bandit](https://bandit.readthedocs.io/) — Python security scanner
- [Pre-commit hooks](https://pre-commit.com/) — automated checks before commit

## Questions?

Open a discussion in #dev-general or tag `@platform-eng` in your MR.