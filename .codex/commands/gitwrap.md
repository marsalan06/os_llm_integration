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
3. Review diffs.
4. Check for sensitive data before staging, including `.env`, credentials, keys, tokens, service account JSON, and curl commands with real secrets.
5. Stage intended files only.
6. Commit and push only when requested.
7. Report commit hash, push result, and clean-tree status.
