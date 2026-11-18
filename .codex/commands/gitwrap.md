# /gitwrap

Follow the global rules in `.codex/README.md`.

Use:
- Agent: `git-agent`
- Skill: `git-closeout`

Purpose:
- Perform repository closeout when the user explicitly requests staging, commit, and push.

Steps:
1. Review git status.
2. Identify intended and unrelated files.
3. Read `docs/DECISION_REGISTER.md`, map only applicable decision IDs, and
   review diffs.
4. Check for sensitive data before staging, including `.env`, credentials, keys, tokens, service account JSON, and curl commands with real secrets.
5. Stage intended files only.
6. Commit only when requested, adding `Decision-ID:` trailers for mapped
   decisions and using the follow-up register backfill workflow when required.
7. Push only when requested.
8. Report commit hashes, decision IDs, push result, and clean-tree status.
