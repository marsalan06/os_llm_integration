# /debug

Follow the global rules in `.codex/README.md`.

Use:
- Agent: `debugger-agent`
- Skill: `debugging`

Purpose:
- Investigate failures, regressions, runtime errors, flaky behavior, or unclear system behavior.

Steps:
1. Capture symptom, expected behavior, actual behavior, and scope.
2. Review logs, running process output, tests, docs, configuration, and relevant code.
3. Ask before running state-changing CLI commands or process operations.
4. Separate facts from hypotheses.
5. Isolate query, logic, configuration, and integration issues.
6. Use screenshots for UI faults when visual state matters.
7. Trace likely root cause.
8. Propose fix direction and focused verification.
