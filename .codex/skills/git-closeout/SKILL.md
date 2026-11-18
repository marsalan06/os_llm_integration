---
name: git-closeout
description: Use for git closeout work including status review, staged-file selection, commits, decision-to-commit traceability, push workflow, and clean-tree reporting.
---

# Git Closeout

Follow the global rules in `.codex/README.md`.

Workflow:
1. Run `git status --short`.
2. Identify intended files and unrelated local changes.
3. Read `docs/54-feature-decision-register.md` and relevant plans when they
   exist. Map only the decision entries actually implemented by the intended
   diff; do not assume every `Pending` entry belongs to the closeout.
4. Review diffs before staging.
5. Check intended files for sensitive data before staging.
6. Stage only intended files.
7. Commit only when requested, using a concise message matching the scope.
8. When the implementation completes one or more mapped decision entries:
   - capture the implementation commit's full hash;
   - update only those entries with the full hash and a short scope note;
   - change an entry to `Implemented` only when its full decision is delivered;
     otherwise retain its current status and identify the partial scope;
   - replace `Pending` verification only with checks that actually ran;
   - review and stage the register update alone, then create a follow-up
     traceability commit such as `docs: link feature decisions to implementation`.
9. Push only when requested, after both commits succeed when a traceability
   commit is required.
10. Report every created commit hash and message, the decision IDs updated, the
    push result, and whether the worktree is clean.

Decision traceability:
- A commit cannot contain its own final hash. Do not amend or recreate the
  implementation commit to insert its hash. Use the follow-up traceability
  commit so the recorded implementation hash remains stable.
- If the user explicitly requires exactly one commit, leave the relevant hashes
  `Pending` and report that a later docs commit is required; never insert a
  guessed or abbreviated future hash.
- Do not backfill unrelated, rejected, superseded, planning-only, or still-open
  decisions merely because they appear in the register.
- If mapping the diff to a decision ID is ambiguous, leave the entry unchanged
  and report the ambiguity rather than guessing.
- If the implementation commit succeeds but the required register update or
  traceability commit fails, do not amend, delete, or push an incomplete
  closeout. Preserve the local implementation commit and report the blocker.

Safety:
- Do not use destructive cleanup commands.
- Do not include unrelated changes unless the user explicitly asks.
- Stop and ask if unexpected changes affect the requested closeout.
- Never stage or commit `.env` files, private keys, tokens, credentials, service account JSON, or local secrets.
- Check `package.json`, markdown files, code files, docs, and scripts for accidental secrets before commit.
- Do not commit curl examples that include real keys, bearer tokens, cookies, signed URLs, private hostnames, or sensitive request payloads.
- Use placeholders such as `<TOKEN>`, `<API_KEY>`, `<PROJECT_ID>`, and `<HOST>` in docs and examples.
