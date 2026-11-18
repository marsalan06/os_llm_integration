# OS-Layer AI Decision Register

**Updated:** 19 November 2026
**Purpose:** Canonical ledger for approved project decisions, their lifecycle,
verification state, and associated implementation commits.

## Reference Hierarchy

1. `docs/MASTER_BLUEPRINT.md` defines product vision, locked principles,
   architecture, safety constraints, and roadmap authority.
2. Phase execution plans, currently `docs/PHASE1_EXECUTION_PLAN.md`, define the
   actionable scope, sequencing, and acceptance contract for a phase.
3. This register is the canonical traceability ledger. It records decision
   status and links each decision to its authoritative source, plan,
   verification, and implementation commits; it does not silently override the
   linked source.
4. `.codex/KNOWLEDGE_BASE.md` is a derived working summary and must be updated
   after canonical decision changes.
5. `.codex/README.md` defines agent workflow and read order, not product policy.

If a register summary conflicts with its linked canonical source, the source
wins for decision content and this register must be corrected. This register
wins for decision lifecycle and commit-traceability state.

## Register Rules

- Use a stable ID such as `S1-FP-001`, where the prefix identifies the stage or
  cross-project scope, the middle token identifies the decision area, and the
  final number is monotonic within that area.
- Record architecture, privacy/security, permissions, data or API contracts,
  platform/runtime boundaries, packaging/release behavior, migrations,
  rollback behavior, and other decisions that constrain multiple changes.
- Routine implementation details and reversible local refactors do not need a
  register entry unless the project owner explicitly promotes them.
- Never change a locked decision silently. Add rationale and approval evidence
  for amendments; preserve superseded decisions and point them to replacements.
- Never invent, predict, or abbreviate a commit hash in this register. Use the
  full hash, followed by a short scope note, only after the commit exists.
- Implementation commits should include one `Decision-ID: <ID>` Git trailer
  per associated decision so all related commits remain discoverable with Git.
- A commit cannot record its own final hash. Create the implementation commit,
  capture its full hash, then backfill the relevant entry in a follow-up
  traceability commit.
- The follow-up documentation commit does not need to record its own hash here;
  its `Decision-ID:` trailer and Git history provide that link and avoid an
  infinite self-reference chain.
- Append corrective, completion, rollback, and reimplementation hashes. Do not
  replace earlier implementation history.
- Mark implementation or verification complete only when the full decision or
  stated verification has actually been delivered.

## Status Vocabulary

### Decision status

- `Proposed`: under discussion and not approved.
- `Accepted`: approved but still intentionally revisable.
- `Locked`: approved project constraint; explicit approval is required to
  amend or supersede it.
- `Superseded`: retained for history and linked to its replacement.
- `Rejected`: considered and intentionally not adopted.

### Implementation status

- `Pending`: no implementation commit recorded.
- `Partial`: some required scope exists; remaining work is stated.
- `Implemented`: the complete decision is represented in implementation.
- `Rolled Back`: implementation was reverted; the decision entry explains its
  current disposition.

### Verification status

- `Pending`: required checks have not run.
- `Partial`: only listed checks have passed; remaining checks are explicit.
- `Verified`: all acceptance evidence defined by the decision has passed.
- `Failed`: evidence disproved the current implementation or assumption and a
  follow-up is required.

## Entry Template

```markdown
## <DECISION-ID> — <Title>

- **Decision status:** Proposed | Accepted | Locked | Superseded | Rejected
- **Decision date:** YYYY-MM-DD
- **Category:** Architecture | Privacy | Runtime | Packaging | UX | ...
- **Approval evidence:** Who approved it and where
- **Canonical source:** File and heading
- **Related plan:** File and heading
- **Affected areas:** Packages, modules, workflows, or stages
- **Implementation status:** Pending | Partial | Implemented | Rolled Back
- **Verification status:** Pending | Partial | Verified | Failed

### Decision

Concise statement of the adopted constraint or behavior.

### Rationale

Why the decision was made.

### Alternatives considered

- Alternative and why it was not selected.

### Implementation commits

- `Pending`, or `<full-commit-hash>` — scope note

### Verification evidence

- `Pending`, or exact checks/results

### Corrective, completion, or rollback commits

- `None`, or `<full-commit-hash>` — scope note
```

## Registered Decisions

## S1-FP-001 — Stage 1 First Principles

- **Decision status:** Locked
- **Decision date:** 2026-11-19
- **Category:** Product architecture, privacy, UX, and resource safety
- **Approval evidence:** Project-owner approval in the Stage 1 planning discussion
- **Canonical source:** `docs/MASTER_BLUEPRINT.md` — “Stage 1 First Principles”
- **Related plan:** `docs/PHASE1_EXECUTION_PLAN.md` — “Locked Stage 1 First Principles”
- **Affected areas:** All Stage 1 components, dependencies, UX, logging, testing, and acceptance
- **Implementation status:** Pending
- **Verification status:** Pending

### Decision

Stage 1 uses explicit invocation, fully local reasoning, read-only behavior,
clipboard-first input, one active request, non-blocking UI work, ephemeral user
content, measurable resource budgets, designed failure states, replaceable
boundaries, and real Windows acceptance evidence.

### Rationale

The MVP must prove a private, lightweight interaction kernel before adding
agent actions, persistence, or broader automation.

### Alternatives considered

- Ambient monitoring — rejected for privacy and idle-resource reasons.
- Cloud fallback — rejected because Phase 1 guarantees local reasoning.
- Automatic paste-back and side effects — rejected to preserve read-only safety.
- Concurrent/queued inference — rejected for predictability and low-RAM behavior.

### Implementation commits

- `Pending`

### Verification evidence

- `Pending` — requires implementation plus interactive target-class Windows testing.

### Corrective, completion, or rollback commits

- `None`

## S1-STACK-001 — Stage 1 Runtime, Stack, And Packaging

- **Decision status:** Locked
- **Decision date:** 2026-11-19
- **Category:** Runtime architecture, dependencies, and packaging
- **Approval evidence:** Project-owner approval in the Stage 1 technical-stack discussion
- **Canonical source:** `docs/MASTER_BLUEPRINT.md` — “Stage 1 Runtime And Stack”
- **Related plan:** `docs/PHASE1_EXECUTION_PLAN.md` — “Locked Tech Choices For Phase 1” and “Locked Folder Structure”
- **Affected areas:** Package layout, runtime dependencies, Ollama boundary, Windows adapters, UI, configuration, build output
- **Implementation status:** Pending
- **Verification status:** Pending

### Decision

Develop with Python 3.11+ and ship a Windows PyInstaller `--onedir --windowed`
app. Use a separately installed local Ollama service through direct `httpx`
calls, Tkinter/TTK, `pystray`, Pillow, `pyperclip`, Win32 hotkey/mutex adapters
through `ctypes`, JSON settings, and a bounded single-worker handoff.

### Rationale

This stack minimizes dependencies, keeps platform behavior explicit, preserves
replaceable boundaries, and supports the low-resource target.

### Alternatives considered

- Ollama SDK or duplicate HTTP clients — rejected to keep one explicit boundary.
- PySide/PyQt, CustomTkinter, or Electron — rejected for the initial lightweight baseline.
- `keyboard`/`pynput` — rejected in favor of the native Windows hotkey API.
- One-file packaging — not selected for the Phase 1 baseline due to startup and diagnostic tradeoffs.

### Implementation commits

- `Pending`

### Verification evidence

- `Pending` — requires source-mode tests, Windows build, integration checks, and 8 GB acceptance.

### Corrective, completion, or rollback commits

- `None`

## S1-DEV-001 — macOS Development And Windows Verification

- **Decision status:** Locked
- **Decision date:** 2026-11-19
- **Category:** Development workflow and platform verification
- **Approval evidence:** Project-owner approval in the macOS development discussion
- **Canonical source:** `docs/MASTER_BLUEPRINT.md` — “Development Host And Windows Evidence”
- **Related plan:** `docs/PHASE1_EXECUTION_PLAN.md` — “macOS Development And Windows Verification”
- **Affected areas:** Platform adapters, development mode, CI/VM checks, packaging, manual acceptance
- **Implementation status:** Pending
- **Verification status:** Pending

### Decision

Use macOS for platform-neutral development with a dev adapter, Windows CI/VM
for automated tests and packaging, interactive Windows for integration, and a
physical 8 GB target-class Windows machine for final performance acceptance.
The accepted Windows package must be built on Windows.

### Rationale

Core development can proceed efficiently on macOS, but Win32 integrations,
packaging, desktop behavior, and low-resource acceptance require Windows
evidence and must not be deferred until release.

### Alternatives considered

- Treating macOS behavior as Windows evidence — rejected because platform APIs and desktop behavior differ.
- Deferring all Windows tests until the end — rejected because integration risk would accumulate.
- Treating CI/VM as final UX/resource evidence — rejected because interactive and physical hardware behavior is required.

### Implementation commits

- `Pending`

### Verification evidence

- `Pending` — no implementation, Windows build, or target-device evidence recorded.

### Corrective, completion, or rollback commits

- `None`
