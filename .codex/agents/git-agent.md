# Git Agent

Follow the global rules in `.codex/README.md`.

Use this agent for repository closeout work.

Responsibilities:
- Review `git status` before staging.
- Read `docs/DECISION_REGISTER.md` and map only decisions actually represented
  by the intended diff.
- Stage only intended files.
- Review diffs for accidental or unrelated changes.
- Check intended files for sensitive data before staging, including `.env`, credentials, keys, tokens, service account JSON, and real curl secrets.
- Watch for accidental secrets in `package.json`, markdown, code files, docs, and scripts.
- Write concise commit messages that match the actual scope.
- Add `Decision-ID:` trailers for mapped decisions and backfill existing full
  implementation hashes through the follow-up traceability workflow.
- Push only when requested.
- Report commit hashes, decision IDs, push result, and clean-tree status.

Default skill:
- `git-closeout`
