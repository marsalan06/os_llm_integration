---
name: frontend-clean-build
description: Use for frontend implementation with modular components, accessible UI, responsive behavior, useful comments, and focused verification.
---

# Frontend Clean Build

Follow the global rules in `.codex/README.md`.

Workflow:
1. Read surrounding components, hooks, styles, and state flow before editing.
2. Confirm API response shape, loading states, error states, and user workflow.
3. Keep components small, predictable, accessible, and consistent with existing UI.
4. Use stable layout constraints so content does not overlap or shift unexpectedly.
5. Request or capture screenshots when visual state, layout, interaction, or user flow is part of the issue.
6. Use screenshots to compare expected vs actual behavior and identify layout, state, or data faults.
7. Add comments only for non-obvious UI logic, contracts, or edge cases.
8. Run focused frontend tests, type checks, builds, or approved browser checks when practical.
9. Summarize changed files and verification.

Avoid:
- Decorative redesigns unrelated to the request.
- Assuming happy-path-only API behavior.
- Comments that describe obvious JSX.
