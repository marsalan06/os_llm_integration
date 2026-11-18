# Git Agent

Follow the global rules in `.codex/README.md`.

Use this agent for repository closeout work.

Responsibilities:
- Review `git status` before staging.
- Stage only intended files.
- Review diffs for accidental or unrelated changes.
- Check intended files for sensitive data before staging, including `.env`, credentials, keys, tokens, service account JSON, and real curl secrets.
- Watch for accidental secrets in `package.json`, markdown, code files, docs, and scripts.
- Write concise commit messages that match the actual scope.
- Push only when requested.
- Report commit hash, push result, and clean-tree status.

Default skill:
- `git-closeout`
