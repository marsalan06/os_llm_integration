---
name: project-help
description: Use to explain the repo-local Codex agents, skills, commands, their responsibilities, and which one to use for a task.
---

# Project Help

Follow the global rules in `.codex/README.md`.

Use this skill to explain the `.codex` scaffold.
Use `.codex/README.md` as the source of truth for the current project map,
agent responsibilities, skill descriptions, command routing, and safety rules.

Explain:
- Agents are roles that decide who should handle the work.
- Skills are reusable playbooks that define how a category of work should be done.
- Commands are manual shortcuts that route a request to an agent and skill.

Available agents:
- `planner-agent`: planning, risk assessment, phase-wise implementation plans.
- `backend-builder-agent`: modular backend implementation and curl/API validation.
- `frontend-builder-agent`: modular frontend implementation and screenshot-aware UI verification.
- `debugger-agent`: log-first debugging, running-process output, query/logic/config isolation.
- `query-agent`: query correctness, indexes, performance, cost, and data-access checks.
- `reviewer-agent`: bugs, security risks, regressions, query issues, and missing tests.
- `git-agent`: status, staging, commits, pushes, and closeout.
- `dependency-agent`: package versions, lockfiles, audits, and compatibility.
- `roman-urdu-tutor-agent`: codebase explanations and walkthroughs in Roman Urdu.
- `help-agent`: explains the `.codex` setup.

Available skills:
- `project-planning`: phase-wise planning and verification.
- `backend-clean-build`: backend modularity, contracts, comments/docstrings, and validation.
- `frontend-clean-build`: frontend modularity, accessibility, screenshots, and verification.
- `debugging`: logs, process output, root-cause isolation, and fix direction.
- `query-checking`: Firestore, BigQuery, logs, metrics, and application query checks.
- `bug-security-review`: bug/security/query/regression review.
- `git-closeout`: staged-file selection, commit hygiene, push reporting.
- `dependency-maintenance`: dependency updates, lockfile sync, audit and compatibility review.
- `roman-urdu-explainer`: Roman Urdu explanations for code, architecture, bugs, and workflows.
- `project-help`: explains agents, skills, and commands.

Available commands:
- `/plan`: use `planner-agent` with `project-planning`.
- `/backend-build`: use `backend-builder-agent` with `backend-clean-build`.
- `/frontend-build`: use `frontend-builder-agent` with `frontend-clean-build`.
- `/debug`: use `debugger-agent` with `debugging`.
- `/query-check`: use `query-agent` with `query-checking`.
- `/review`: use `reviewer-agent` with `bug-security-review`.
- `/gitwrap`: use `git-agent` with `git-closeout`.
- `/deps`: use `dependency-agent` with `dependency-maintenance`.
- `/explain`: use `roman-urdu-tutor-agent` with `roman-urdu-explainer`.
- `/help`: use `help-agent` with `project-help`.

Output:
- Give a concise list by default.
- Recommend the best command for the user's described task.
- Include one or two examples when helpful.
