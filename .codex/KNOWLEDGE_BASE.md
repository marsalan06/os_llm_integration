# OS-Layer AI Knowledge Base

**Updated:** 19 November 2026  
**Project state:** Pre-implementation  
**Active milestone:** Stage 1 / Phase 1 MVP

This is the compact, project-local context for agents and contributors. It is
derived from `MASTER_BLUEPRINT.md` and `PHASE1_EXECUTION_PLAN.md`; it does not
replace either source of truth.

## Product In One Paragraph

OS-Layer AI is an offline-first Windows super-user assistant for modest
consumer hardware. It lives in the system tray, is invoked by a global hotkey,
captures copied clipboard text (with selection as best effort), sends it to a local quantized language
model, and displays a concise answer in a small Orb-adjacent response panel.
Its identity is a quiet Windows utility—not a full chat client and not
primarily a coding agent.

## Users And Constraints

- Target OS: Windows 10 and Windows 11.
- Target hardware: typical Intel Core i5 class machines, 4–8 GB RAM, integrated
  Intel graphics.
- Inference must remain local for Phase 1.
- Model format/size strategy: GGUF Q3/Q4, with Q4 or lighter recommended for
  initial testing.
- Low idle footprint and a responsive desktop take priority over model size or
  maximum answer quality.
- The UI must not freeze while a model loads or generates.

## Active Phase Contract

The governing decision is `S1-FP-001`, locked by the project owner on
19 November 2026. Implementation and real-device verification are pending.
The runtime/packaging decision is `S1-STACK-001`, and the macOS-development /
Windows-verification decision is `S1-DEV-001`; both were approved on the same
date and remain pending implementation and verification.

### Required

- Configurable system-wide hotkey.
- Explicit hotkey invocation with no continuous clipboard, screen, microphone,
  or ambient-content monitoring.
- Reliable clipboard text capture, with selected-text capture attempted only
  as a best-effort enhancement.
- Local inference through Ollama as the preferred backend.
- Built-in Summarize, Rewrite, Explain, and Fix Grammar modes plus a short
  custom instruction.
- Compact, non-intrusive response UI with Copy and Close.
- System tray menu with Show/Status, Settings as needed, and Quit.
- Model selection and clear loaded/unloaded/error status.
- Configurable startup loading and idle unloading.
- Graceful errors for empty input, missing model, stopped Ollama, timeouts,
  hotkey conflicts, and overlapping requests.
- One active request; repeated activation shows “already processing” without
  queueing or replacing the current request.
- Low-resource behavior validated on a real 8 GB Windows machine.

### Forbidden In Phase 1

- Reading or writing files as an assistant capability.
- PowerShell, shell, subprocess, or system-action execution.
- Web search, internet tools, or silent cloud inference fallback.
- Vector database, RAG, knowledge graph, project indexing, or long-term memory.
- Multi-turn chat history beyond the current request; at most the last response
  may be retained transiently for the UI.
- Voice features, coding-agent workflows, full installer, or auto-update.
- A complex future permission framework when Phase 1 has no mutations.
- Automatic paste-back or replacement in the foreground application.
- Persistent or content-logged clipboard input, prompts, or generated output.

Out-of-scope feature requests go to Stage 2 or later unless they fix a critical
bug or usability blocker in the core interaction loop.

## Request Lifecycle

```text
startup
  → load validated config
  → enforce single instance
  → start tray and UI loop
  → register hotkey

hotkey press
  → if busy, show "already processing" and stop this activation
  → capture clipboard text or best-effort selected text
  → reject empty/non-text input gently
  → show thinking state
  → dispatch to the single worker without blocking UI
  → apply selected prompt template
  → POST non-streaming JSON to 127.0.0.1:11434/api/generate
  → render result or actionable error
  → allow Copy or Close
  → never paste automatically

idle timeout
  → unload model when configured
  → update tray/Orb status
```

## Architecture And Ownership

| Area | Responsibility | Important boundary |
|---|---|---|
| App entry point | Compose services and own startup/shutdown | No feature logic in UI callbacks |
| Config | Defaults, validation, JSON persistence | Store under `%LOCALAPPDATA%/OSLayerAI/` on Windows |
| Hotkey | Win32 `RegisterHotKey` adapter | Handle conflicts; do not perform inference inline |
| Clipboard/selection | Return normalized text or a typed empty/error result | Clipboard-first fallback must be documented |
| Orchestrator | One request pipeline, prompt selection, busy rejection, cancellation, errors | Never block the UI thread or queue concurrent work |
| Ollama client | Direct local HTTP through one `httpx.AsyncClient` | Endpoint is `http://127.0.0.1:11434` |
| Model manager | Discover, load, unload, and report model state | Be honest about what the backend can observe/control |
| Tray/Orb | Status and lifecycle controls | Keep always-on UI minimal |
| Response panel | Result, Copy, Close, optional always-on-top | Avoid aggressive focus stealing |
| Logging | Local structured diagnostics | Content logging is off by default |

Use one bounded handoff plus async work between UI callbacks and inference. It
must not become a user-request queue. If a chosen Windows/UI library is
blocking, isolate it rather than pretending it is async.

## Prompt And Content Safety

- Built-in prompts should request concise, directly useful output.
- Selected/clipboard text is untrusted user data, not an instruction with the
  same authority as the system prompt.
- Keep instruction and content fields structurally distinct where supported;
  otherwise delimit content clearly.
- Do not request or expose hidden chain-of-thought.
- Do not persist or log source text and model output by default.
- Phase 1's strongest safety property is absence of side effects; preserve it.

## Configuration Baseline

The minimum settings are:

- `hotkey`
- `default_model`
- `default_action`
- `auto_load_model`
- `idle_unload_timeout`

Likely generation controls such as temperature and output-token limit belong
at the inference boundary, but their user-facing exposure is not locked.
Settings must have safe defaults and recover cleanly from missing or invalid
configuration. Windows persists JSON under `%LOCALAPPDATA%/OSLayerAI/`; macOS
development uses an isolated dev path rather than pretending to use AppData.

## Local Ollama Contract

- Startup/model discovery uses `GET /api/tags`.
- Generation uses `POST /api/generate` with `model`, separate `system` and
  `prompt`, `stream: false`, `keep_alive`, and bounded generation options.
- The app consumes response/completion fields plus duration/token metadata for
  diagnostics and acceptance measurements, without logging content.
- Idle unload uses `keep_alive: 0`.
- Connection refusal, missing model, timeout, invalid response, and incomplete
  generation become distinct actionable product errors.

## Locked Runtime And Tooling

- Python 3.11+ in source mode via `python -m oslayer`.
- Windows release via PyInstaller `--onedir --windowed`; full installer and
  auto-update remain Stage 2.
- Ollama installed separately and called only over local HTTP.
- Runtime dependencies: `httpx`, `pystray`, Pillow, and `pyperclip`.
- Standard-library Tkinter/TTK UI, JSON config, logging, `asyncio`, threads,
  bounded queue, `ctypes`, dataclasses, enums, paths, and protocols.
- Win32 `RegisterHotKey` and named mutex through isolated `ctypes` adapters.
- Development dependencies: `pytest`, `pytest-asyncio`, and `ruff`.

Do not add the Ollama SDK, `requests`, `keyboard`, `pynput`, CustomTkinter,
PySide/PyQt, TOML helpers, Pydantic, `psutil`, or persistence libraries unless
implementation evidence proves the locked baseline cannot meet a requirement.
Pin exact compatible versions after the macOS spike and Windows verification.

## Development And Release Environments

- macOS is the daily development host for core contracts, config, prompts,
  orchestration, Ollama HTTP, worker logic, tests, and basic Tkinter layout.
- `platform/dev` provides a development trigger/paths; it is not a macOS
  product commitment.
- Windows-specific adapters remain isolated and conditionally composed.
- Windows CI or a VM runs imports/tests, startup smoke checks, and PyInstaller
  builds for meaningful integration slices.
- Interactive Windows 10/11 verifies hotkey, tray, clipboard across apps,
  focus, DPI/multi-monitor behavior, shutdown, and packaged-app behavior.
- A physical 8 GB target-class Windows machine supplies final latency, CPU/RAM,
  stability, and unload/reload acceptance evidence.
- PyInstaller is not used as a cross-compiler; the accepted `.exe`/onedir
  package is built on Windows.

## Definition Of Done

Phase 1 is complete only when all of the following are true:

1. A configurable hotkey works from common Windows applications.
2. Clipboard or selection text is captured predictably with a clear fallback.
3. A local model answers within an acceptable time on an 8 GB machine.
4. The compact UI displays the answer without freezing or disruptive focus
   changes.
5. The tray application remains stable with low idle resource usage.
6. Model unload/reload and status reporting behave correctly.
7. Minimal settings work and invalid configuration fails safely.
8. Expected error states do not crash or hang the app.
9. A non-technical user can follow the README, start the prerequisites, invoke
   the tool, and get a useful result without opening a full chat application.

## Minimum Test Matrix

| Concern | Cases |
|---|---|
| Host apps | Notepad, Chrome, VS Code, Word, File Explorer |
| Input | Empty, long, Unicode, multiline, non-text, stale clipboard |
| Backend | Ollama stopped, model absent, model loading, timeout, malformed response |
| Model lifecycle | List, switch, unload on idle, reload on demand |
| Concurrency | Repeated hotkey, request already running, close/quit during request |
| UI | Ready/thinking/error, Copy, Close, tray actions, focus behavior |
| Process | Single-instance startup and clean shutdown |
| Resources | Idle RAM/CPU, inference RAM/CPU, 8 GB responsiveness, recovery after failure |

Mocks can validate logic, but they do not replace Windows integration and
low-memory acceptance testing.

## Roadmap Boundaries

- **Stage 1:** read-only text helper and interaction kernel.
- **Stage 2:** installer, settings polish, permission-system foundation, action
  log, and permissioned simple file reading.
- **Stage 3:** text-file read/write with diff preview, vector memory, basic
  knowledge graph, project detection, and idle-only indexing.
- **Stage 4:** coding-oriented multi-file awareness, controlled PowerShell, and
  explicitly gated internet tools.
- **Stage 5:** deeper Windows integration, reliability, performance, edge-case
  polish, and a broader permissioned action set.

No later-stage feature should weaken the deny-by-default, explicit-consent,
fully-audited action model.

## Known Risks And Required Mitigations

| Risk | Required response |
|---|---|
| Model exhausts RAM or freezes the desktop | Recommend small quantized models, serialize work, monitor behavior, unload on idle |
| Hotkey is unreliable or conflicts | Test competing libraries/native hooks and make the binding configurable |
| Selected text cannot be captured consistently | Make clipboard the dependable baseline and document best-effort selection behavior |
| UI blocks during inference | Keep inference/model work outside the UI thread and test cancellation/shutdown |
| Ollama/model is unavailable | Show a concise actionable local error; never fall back to cloud silently |
| Multiple instances duplicate hooks/tray icons | Enforce a single-instance policy |
| Prompt content is treated as authority | Delimit untrusted text and retain a strict system instruction |
| Scope drifts into an agent or full chat app | Check every feature against the Phase 1 forbidden list and product identity |

## Open Decisions

These require implementation spikes or real-device evidence:

1. Optional selected-text capture technique that preserves clipboard as the
   reliable baseline.
2. Model preloading default and idle timeout default.
3. Exact measurable resource, latency, and input/output acceptance budgets.
4. Final recommended model list based on latency, quality, and peak memory.
5. Exact compatible dependency pins after Windows verification.

Record settled decisions in the canonical plan or a dedicated decision record,
then update this knowledge base rather than allowing code and docs to diverge.

## Maintenance Rule

Update this file whenever either canonical document changes scope, architecture,
roadmap, safety constraints, definition of done, or a previously open decision.
Keep implementation status factual: proposed files and libraries are not
described as implemented until they exist and have been verified.
