---
name: bug-security-review
description: Use for reviewing code, diffs, plans, and queries for bugs, security risks, regressions, missing tests, and maintainability issues.
---

# Bug And Security Review

Follow the global rules in `.codex/README.md`.

Workflow:
1. Inspect the changed files and relevant surrounding code.
2. Lead with findings, ordered by severity.
3. Include exact file and line references where possible.
4. Separate confirmed issues from assumptions and open questions.
5. Check query correctness, auth/permission boundaries, input validation, error handling, and test coverage.
6. Keep the summary brief and secondary to findings.

Finding format:
- Severity
- File reference
- Problem
- Why it matters
- Suggested fix

If no issues are found, say so and mention any remaining test gaps or residual risk.
