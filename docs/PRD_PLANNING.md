# PRD planning (kickoff)

This document bootstraps **product requirements planning** for workstreams that touch this repository. It aligns with the agent conventions in `claude.md` and the task lifecycle in `task_replan_planner.md`.

## Purpose

- Capture **what** we are building or changing before implementation spikes.
- Tie goals to **measurable acceptance criteria** and **out of scope** boundaries.
- Give reviewers and agents a single place to align on scope before PRs multiply.

## Document lifecycle

| Phase | Owner | Output |
|-------|--------|--------|
| Discovery | Product / tech lead | Problem statement, users, constraints |
| Definition | Team | Goals, non-goals, AC, risks |
| Breakdown | Coordinator / agents | IDEAL task plan (see `task_replan_planner.md`) |
| Delivery | Implementers | Small PRs, traced to AC |

Update this file when scope shifts; use `task_replan_planner.md` when existing tasks must be reconciled with a new IDEAL plan.

## PRD skeleton (copy per initiative)

Use one subsection per initiative, or split into `docs/prd/<initiative>.md` when this file grows too large.

### 1. Summary

One paragraph: the change in plain language.

### 2. Goals

- …

### 3. Non-goals

- …

### 4. Users and scenarios

- Primary users:
- Primary workflows:

### 5. Requirements

Functional:

- …

Non-functional (performance, security, compatibility):

- …

### 6. Acceptance criteria

Numbered, testable statements (maps cleanly to tasks in Phase 2 of `task_replan_planner.md`).

1. …

### 7. Dependencies and risks

- Dependencies:
- Risks and mitigations:

### 8. Open questions

- …

## References

- `claude.md` — agent and workflow conventions
- `dev_best_practices.md` — PR size, review hygiene
- `agentic_workflow_helper.md` — tool usage and context
- `task_replan_planner.md` — replanning when tasks already exist
