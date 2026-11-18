# OS-Layer AI Codex Guide

This folder contains the repo-local agents, skills, and command shortcuts for
working on OS-Layer AI. Use this guide for workflow selection and
`.codex/KNOWLEDGE_BASE.md` for the compact project context every task should
preserve.

## Project Purpose

OS-Layer AI is a private, offline-first Windows super-user assistant designed
for ordinary Windows 10/11 computers, including Intel Core i5 systems with
4–8 GB RAM and integrated graphics. It should feel like a quiet system utility:
it lives in the tray, appears as a small Orb or compact panel when invoked, and
uses local inference rather than a cloud service.

The current target is **Stage 1 / Phase 1 MVP**: selected or clipboard text is
sent to a local quantized model through a configurable global hotkey, and the
answer is shown in a lightweight floating response panel. Phase 1 is strictly
read-only and has no file, shell, internet, or system mutation capabilities.

## Canonical Documents

Read project context in this order:

1. `MASTER_BLUEPRINT.md` — source of truth for vision, locked principles,
   architecture, safety posture, and the multi-stage roadmap.
2. `PHASE1_EXECUTION_PLAN.md` — source of truth for the active MVP scope,
   definition of done, component responsibilities, sequence, and test matrix.
3. `.codex/KNOWLEDGE_BASE.md` — concise operational summary derived from the
   two canonical documents; update it when either source changes.
4. Relevant source, tests, configuration, and nearby documentation after those
   files exist.

If summaries conflict with a canonical document, the canonical document wins.
If the two canonical documents conflict, preserve the master blueprint's
locked architectural constraints and flag the conflict before implementation.

## Current Repository State

The repository is currently in pre-implementation planning. At the time of
this guide's update, it contains the two canonical planning documents and the
`.codex` workflow scaffold; the locked `src/`, `tests/`, and `assets/` trees
have not yet been created.

The locked Phase 1 package shape (`S1-STACK-001`) is:

```text
src/oslayer/
├── __main__.py
├── main.py
├── core/
│   ├── contracts.py
│   ├── config.py
│   ├── paths.py
│   ├── prompts.py
│   ├── orchestrator.py
│   └── worker.py
├── inference/
│   ├── base.py
│   ├── ollama_client.py
│   └── model_manager.py
├── platform/
│   ├── base.py
│   ├── dev/
│   │   ├── hotkey.py
│   │   └── paths.py
│   └── windows/
│       ├── hotkey.py
│       ├── clipboard.py
│       ├── single_instance.py
│       └── paths.py
├── ui/
│   ├── app.py
│   ├── tray.py
│   ├── orb.py
│   ├── response_panel.py
│   └── settings_panel.py
└── utils/
    └── logging.py
```

This structure is locked but not implemented yet. The macOS `platform/dev`
adapter supports development and does not make macOS a Stage 1 product target.

## Locked Product And Architecture Rules

- Keep reasoning local and offline. Do not introduce an implicit cloud
  fallback.
- Activate only from an explicit user hotkey; do not continuously monitor the
  clipboard, screen, microphone, or other user content.
- Optimize for 4–8 GB RAM systems with Q3/Q4 GGUF models and deliberate model
  load/unload behavior.
- Keep the product tray-first, hotkey-first, and almost invisible by default.
- Never block the UI thread with clipboard, inference, model-management, or
  orchestration work.
- Treat Phase 1 input as text data and keep the prompt boundary explicit.
- Treat copied clipboard text as the reliable contract and selected-text
  capture as best-effort only.
- Allow one active request. A repeated hotkey while busy shows “already
  processing” and neither queues nor replaces the current request.
- Phase 1 performs no file writes, command execution, web access, persistent
  memory, project detection, or multi-turn conversation.
- Phase 1 does not automatically paste or replace foreground text; Copy remains
  an explicit user action.
- Keep clipboard input and generated output ephemeral and out of logs by
  default.
- Future side effects must pass through deny-by-default permission checks and
  a local action log; do not prematurely add those actions to Phase 1.
- Prefer the simplest reliable Windows-compatible dependency and avoid heavy
  web/Electron architecture for the core application.
- Treat expected failures as visible product states and require real Windows
  10/11 evidence for integration and acceptance decisions.

## Phase 1 Component Boundaries

- `main.py`: application composition, startup, shutdown, and single-instance
  coordination.
- `core/contracts.py`: typed request, result, state, error, and adapter contracts.
- `core/config.py`: validated JSON settings with defaults for hotkey, model, action,
  startup loading, and idle unload timeout.
- `core/orchestrator.py`: single-request pipeline, prompt-mode selection, busy
  rejection, and typed result/error routing.
- `core/worker.py`: one background async worker with bounded handoff.
- `platform/windows/hotkey.py`: native `RegisterHotKey` registration, conflict
  handling, Windows message processing, and unregister behavior.
- `platform/windows/clipboard.py`: clipboard-first text capture; selection
  capture is a best-effort enhancement with a documented fallback.
- `platform/windows/single_instance.py`: named-mutex duplicate-instance guard.
- `platform/dev/`: macOS development trigger/paths without pretending to
  validate Windows behavior.
- `inference/ollama_client.py`: local Ollama HTTP boundary, timeouts, errors,
  and generation calls.
- `inference/model_manager.py`: model discovery, status, load/unload lifecycle,
  and missing-model handling.
- `ui/app.py`: the single Tk root and main-thread UI scheduling.
- `ui/tray.py`: tray status and Show/Settings/Quit actions.
- `ui/orb.py`: ready/thinking/error state indication.
- `ui/response_panel.py`: compact response display with Copy and Close actions,
  without aggressive focus stealing.
- `utils/logging.py`: structured local diagnostic logs; never log sensitive
  clipboard or prompt content by default.

## Decisions Still Open

Do not present these as finalized until implementation evidence settles them:

- Preloading the default model versus loading it on first request.
- Idle unload timeout default.
- Exact resource, latency, and input/output acceptance budgets.
- Exact recommended models and dependency version pins after Windows testing.
- Optional direct selected-text capture technique.

The locked baseline is Python 3.11+, Tkinter/TTK, native Win32 hotkey and mutex
adapters, direct Ollama HTTP through `httpx`, JSON settings, `pystray`, Pillow,
`pyperclip`, and a bounded single-worker handoff. The Phase 1 Windows package
uses PyInstaller `--onedir --windowed` and must be built on Windows.

## Implementation Order

Keep work incremental and validate each boundary before adding the next:

1. Project skeleton and configuration loading.
2. Ollama client and a CLI generation path.
3. Model discovery, status, load, and unload behavior.
4. Clipboard capture and empty/non-text handling.
5. CLI version of the complete orchestrator flow.
6. Tray integration.
7. Global hotkey integration and conflict handling.
8. Orb and response panel.
9. End-to-end wiring, concurrency, and error states.
10. Idle unloading and status indicators.
11. Minimal settings UI.
12. Edge-case polish and real Windows 8 GB acceptance testing.

Do not begin with the floating UI before the local CLI request path works
reliably.

## Verification Expectations

Match verification to the changed boundary:

- Pure logic/config: focused unit tests on the development host.
- Ollama integration: local API contract tests with clear handling for a
  stopped service and missing model; ask before starting or changing external
  processes.
- Windows integrations: verify on Windows 10/11, not only through mocks or a
  non-Windows host.
- macOS development: run platform-neutral core, Ollama, worker, and basic UI
  tests through the dev adapter; never claim this validates Win32 behavior.
- Windows CI/VM: run Windows imports/tests, startup smoke checks, and the
  PyInstaller `--onedir --windowed` build for meaningful integration slices.
- Hotkeys: test Notepad, Chrome, VS Code, Word, and File Explorer, including
  conflicts and repeated presses.
- Clipboard: test empty, long, non-text, Unicode, and stale clipboard content.
- UI: test loading/error/ready states, Copy, Close, focus behavior, tray exit,
  and multiple-instance handling.
- Resource behavior: measure idle RAM, inference-time RAM, responsiveness,
  unload/reload, and failure behavior on a real 8 GB machine.
- Final acceptance: use an interactive, physical target-class Windows machine;
  CI/VM results do not replace focus, tray, DPI, hotkey, or resource evidence.
- Docs-only changes: check headings, links, paths, terminology, and consistency
  with both canonical documents.

Phase 1 is not done until the complete select/copy → hotkey → local response
loop is stable, lightweight, and understandable to a non-technical user.

## Global Work Rules

- Preserve unrelated user changes and keep edits within the requested scope.
- Read the canonical documents before making product, architecture, scope, or
  sequencing claims.
- Distinguish proposed structure from implemented behavior.
- Keep the UI responsive by isolating blocking libraries from its thread.
- Fail clearly when Ollama is stopped, a model is absent, the clipboard is
  empty, a hotkey conflicts, or a request times out.
- Avoid logging clipboard text, prompt text, generated answers, credentials,
  or machine-specific private data by default.
- Ask before installing dependencies, starting/stopping services, downloading
  models, packaging executables, or performing any external/system mutation.
- Report changed files, verification performed, and any Windows-only checks
  that remain outstanding.

## Agents, Skills, And Commands

The `.codex` scaffold supplies reusable roles. Select the smallest workflow
that matches the task:

| Need | Agent / skill | Shortcut |
|---|---|---|
| Plan a feature, decision, or implementation slice | `planner-agent` / `project-planning` | `/plan` |
| Build Python services, orchestration, config, or inference code | `backend-builder-agent` / `backend-clean-build` | `/backend-build` |
| Build the Orb, tray, settings, or response UI | `frontend-builder-agent` / `frontend-clean-build` | `/frontend-build` |
| Diagnose runtime, integration, or test failures | `debugger-agent` / `debugging` | `/debug` |
| Review changes for bugs, safety, regressions, and missing tests | `reviewer-agent` / `bug-security-review` | `/review` |
| Maintain Python dependencies and pins | `dependency-agent` / `dependency-maintenance` | `/deps` |
| Explain architecture or code in Roman Urdu | `roman-urdu-tutor-agent` / `roman-urdu-explainer` | `/explain` |
| Explain the local Codex scaffold | `help-agent` / `project-help` | `/help` |
| Review git state or commit/push when explicitly requested | `git-agent` / `git-closeout` | `/gitwrap` |

The generic `query-agent` and `/query-check` remain available for future
application data-access work, but Phase 1 has no database or remote query
surface.

## Closeout

At the end of every task, report:

- Files changed and the behavior or knowledge they now represent.
- Verification commands/checks and their results.
- Checks not run, especially Windows-only, Ollama, low-memory, or UI checks.
- Any decision or implementation detail that remains provisional.
