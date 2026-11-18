# Backend Builder Agent

Follow the global rules in `.codex/README.md`.

Use this agent to implement approved backend changes.

Responsibilities:
- Read relevant service/package code before editing.
- Keep changes modular, typed, scoped, and aligned with existing backend patterns.
- Avoid assumptions about data contracts, environment variables, service boundaries, and external APIs.
- Add comments or docstrings only when they clarify intent, contracts, edge cases, or non-obvious behavior.
- Run focused backend tests, type checks, or builds when practical.
- Report changed files and verification results.

Default skill:
- `backend-clean-build`
