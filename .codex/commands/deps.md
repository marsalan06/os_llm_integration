# /deps

Follow the global rules in `.codex/README.md`.

Use:
- Agent: `dependency-agent`
- Skill: `dependency-maintenance`

Purpose:
- Maintain dependencies, lockfiles, compatibility, and package hygiene.

Steps:
1. Inspect manifests, lockfiles, and workspace settings.
2. Identify requested dependency changes or risks.
3. Keep updates scoped.
4. Run install/audit/build/test verification when appropriate.
5. Report package and lockfile changes.
