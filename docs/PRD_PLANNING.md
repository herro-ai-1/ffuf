# PRD planning — kickoff

This document starts **structured product requirements planning** for ffuf workstreams (features, UX, compatibility, and release expectations). Treat it as a living outline: refine sections as decisions land, and link out to issues or design notes.

## 1. Problem and context

- **Users:** security testers and developers running HTTP fuzzing at scale.  
- **Current baseline:** fast concurrent fuzzer with matchers/filters, recursion, interactive mode, multiple output formats (see `README.md`).  
- **Why plan:** align roadmap candidates with maintenance cost, backward compatibility, and test coverage before large changes.

## 2. Goals (draft)

| Horizon | Candidate goals |
|--------|------------------|
| Near term | Clarify highest-priority bugs vs. feature asks; keep CLI stability for scripted use. |
| Medium | Improve observability (logging, metrics hooks) without hurting throughput — requires PRD tradeoffs. |
| Long | Optional plugin or extension points only if demand and maintainer capacity are clear. |

## 3. Non-goals (initial)

- Rewriting core concurrency without a written performance budget and benchmarks.  
- Breaking default CLI behavior without a major version and migration notes.

## 4. Personas and scenarios

1. **CLI automation** — ffuf in CI/scripts; stable flags and exit codes.  
2. **Interactive triage** — pause, filter tuning, queue management.  
3. **Large campaigns** — rate limits, timeouts, wordlist modes, output to disk.

## 5. Requirements backlog (to prioritize)

Use your issue tracker to promote items from this pool:

- **Functional:** new matchers/filters, encoding edge cases, proxy/TLS scenarios.  
- **Reliability:** panics, resource cleanup, cross-platform paths.  
- **UX:** help text, config file precedence, error messages.  
- **Docs:** wiki parity with README, examples for new flags.

## 6. Success metrics

Define per initiative (examples): test coverage on touched packages, no regressions in `go test ./...`, benchmark thresholds for requests/sec on a reference fixture, or user-reported defect counts for an area.

## 7. Risks and dependencies

- **Go version** — match `go.mod` and CI (`/.github/workflows`).  
- **Third-party modules** — vet licenses and minimal version bumps.  
- **Platforms** — Windows vs. POSIX differences in `pkg/interactive` and paths.

## 8. Process

1. **Intake** — capture idea in an issue with user story + acceptance criteria.  
2. **PRD slice** — for larger work, add a subsection here or a linked `docs/prd/<name>.md` with scope, API/CLI diff, and test plan.  
3. **Replanning** — follow `task_replan_planner.md` when scope or blockers change.  
4. **Shipping** — update `CHANGELOG.md` under `master` → `New` / `Changed` / `Fixed` as appropriate.

## 9. Open questions

- Which release line targets the next sizable feature?  
- Are there sponsored or downstream integrations that constrain flag names or JSON output shape?  
- What benchmark corpus represents “must not regress”?

---

*Bootstrap aligns with repository agent guidelines in `claude.md` and team helpers (`dev_best_practices.md`, `agentic_workflow_helper.md`).*
