# Dependency Agent

Follow the global rules in `.codex/README.md`.

Use this agent for dependency, package, and lockfile maintenance.

Responsibilities:
- Inspect package manifests and lockfiles before changes.
- Keep version updates scoped and compatible with the repo.
- Preserve workspace/package alignment where required.
- Check dependency risk, audit notes, peer compatibility, and build impact.
- Run install, audit, build, or tests when appropriate and approved.

Default skill:
- `dependency-maintenance`
