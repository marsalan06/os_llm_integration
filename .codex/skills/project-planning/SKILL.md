---
name: project-planning
description: Use for planning project changes by reading instructions, docs, code, risks, assumptions, and verification needs before implementation.
---

# Project Planning

Follow the global rules in `.codex/README.md`.

Workflow:
1. Read `.codex/README.md`, `MASTER_BLUEPRINT.md`,
   `PHASE1_EXECUTION_PLAN.md`, and `.codex/KNOWLEDGE_BASE.md` first.
2. Inspect relevant source, tests, configuration, and nearby docs before making
   implementation claims. Treat proposed paths as unimplemented until they
   exist.
3. Restate the goal and identify affected areas.
4. List assumptions, risks, dependencies, and unknowns.
5. Do not silently overturn a locked master-blueprint decision. For a critical
   architecture, privacy, permission, data, action, packaging, or rollout
   decision, update the appropriate canonical document or create a focused
   decision record when the user approves the change.
6. Record a critical decision's status, rationale, alternatives, affected
   areas, approval evidence, verification state, and implementation reference.
   Update `.codex/KNOWLEDGE_BASE.md` after the canonical record changes.
7. Break the work into modular phases that can be implemented and tested independently.
8. For each phase, define the implementation scope, expected result, validation method, and rollback or follow-up concern.
9. Keep Phase 1 work aligned with `PHASE1_EXECUTION_PLAN.md`. Put future
   detailed plans in an appropriate `docs/` location once that structure
   exists; do not create competing root-level sources of truth.
10. Include CLI commands only as proposed verification steps; ask before running commands that change state, start processes, hit external systems, or require credentials.
11. Ask for approval before implementation unless the user explicitly asked to proceed.

Output:
- Goal
- Context reviewed
- Phase-wise plan
- Risks or assumptions
- Phase-wise verification
- Canonical decision or knowledge-base updates, when applicable
