---
name: backend-clean-build
description: Use for backend implementation with scoped changes, modular design, typed contracts, useful comments/docstrings, and focused verification.
---

# Backend Clean Build

Follow the global rules in `.codex/README.md`.

Workflow:
1. Read surrounding service/package code before editing.
2. Confirm data contracts, environment variables, and service boundaries from source or docs.
3. Keep changes small, modular, typed, and aligned with existing backend patterns.
4. Prefer shared helpers only when they reduce real duplication or clarify behavior.
5. Add comments/docstrings only for intent, contracts, edge cases, or non-obvious logic.
6. Make each backend feature independently validatable where practical, including a curl example or equivalent CLI/API check for endpoint behavior.
7. Run focused tests, type checks, builds, or approved curl/API checks for the touched backend area.
8. Summarize changed files and verification.

Avoid:
- Guessing API shapes or database schemas.
- Broad refactors unrelated to the request.
- Comments that merely repeat the code.
- Shipping a backend endpoint without a clear validation path.
