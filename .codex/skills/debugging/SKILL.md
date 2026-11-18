---
name: debugging
description: Use for investigating backend, frontend, data, runtime, integration, configuration, and test failures by reproducing symptoms, tracing causes, and proposing root-cause fixes.
---

# Debugging

Follow the global rules in `.codex/README.md`.

Workflow:
1. Capture the symptom, expected behavior, actual behavior, and scope.
2. Reproduce the issue or find the closest available evidence.
3. Treat logs as critical evidence: read relevant application logs, process output, test output, and service logs before deciding on a fix.
4. If a process is running, inspect its current logs/output when available; ask before starting, stopping, restarting, or running CLI commands that change state.
5. Read relevant tests, docs, configuration, and surrounding code.
6. Separate facts from hypotheses.
7. Isolate query issues, logical issues, configuration issues, and integration issues before editing.
8. Trace control flow, data flow, environment variables, permissions, and external dependencies.
9. For frontend issues, request or capture screenshots when visual state, layout, or user flow matters.
10. Identify the most likely root cause and alternatives.
11. Propose the smallest durable fix and focused verification.

Output:
- Symptom
- Evidence reviewed
- Logs reviewed
- Root-cause hypothesis
- Fix direction
- Verification plan

Avoid:
- Guessing from one stack trace without checking surrounding code.
- Patching symptoms before understanding the cause.
- Broad unrelated refactors during investigation.
- Running state-changing CLI commands without approval.
