---
name: dependency-maintenance
description: Use for package dependency updates, lockfile synchronization, audit review, version compatibility, and workspace package hygiene.
---

# Dependency Maintenance

Follow the global rules in `.codex/README.md`.

Workflow:
1. Inspect relevant `package.json`, lockfiles, workspace settings, and build scripts.
2. Identify direct vs transitive dependency changes.
3. Check version compatibility, peer dependencies, runtime impact, and audit/security notes.
4. Keep updates scoped to the requested package or risk.
5. Refresh lockfiles only when needed.
6. Run install, audit, tests, or builds when appropriate and approved.
7. Summarize changed manifests, lockfiles, and verification.

Avoid:
- Opportunistic unrelated upgrades.
- Lockfile churn without a package reason.
- Changing service version alignment unless requested.
