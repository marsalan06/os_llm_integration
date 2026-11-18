# Phase 1 Execution Plan — OS-Layer AI
**Parent Document:** `docs/MASTER_BLUEPRINT.md`
**Phase:** Stage 1 – MVP  
**Goal of this document:** Exact, actionable specification for the first shippable version.  
**Last Updated:** 19 November 2026

---

## 1. Phase 1 Goal (One Sentence)

Deliver a **reliable, lightweight, hotkey-driven text helper** that lives in the Windows system tray, uses a local quantized model, and feels almost invisible — proving the core interaction loop before any file or system mutation features are added.

---

## 2. Exact Scope of Phase 1

### In Scope (Must Have)

- Global hotkey that works system-wide on Windows 10/11
- Reliably read copied clipboard text; selected-text capture is a best-effort
  enhancement where Windows and the foreground application permit it
- Send that text + a simple system prompt to a local model (via Ollama preferred)
- Show response in a compact floating panel / near a small Orb
- System tray icon with basic menu (Show Orb, Status, Quit)
- Model status awareness (loaded / unloaded / error)
- Ability to choose / switch between a small set of local models
- Basic settings: hotkey, default model, response style (summarize / rewrite / explain / custom)
- Completely offline reasoning
- Runs on 8 GB RAM machines without freezing (Q4 or lighter models)

### Explicit Non-Goals (Do Not Build in Phase 1)

- File read or write of any kind
- PowerShell or shell command execution
- Vector database / RAG / long-term memory
- Project detection
- Multi-turn conversation history beyond the current request
- Voice input/output
- Internet tools or web search
- Full installer / auto-updater (can be a simple runnable package)
- Complex permission system (not needed yet because no mutations)
- Coding-agent features (multi-file edits, terminal, etc.)
- Automatic paste-back or replacement of text in the foreground application
- Continuous clipboard, screen, microphone, or ambient-content monitoring

**Why this boundary exists:**  
We must first make the “copy text → hotkey → useful answer appears” loop feel
excellent and lightweight. Best-effort direct selection capture may shorten
that flow later; everything else builds on the reliable clipboard baseline.

---

## 3. How Phase 1 Fits the Master Blueprint

| Master Blueprint Element | Phase 1 Realization |
|--------------------------|---------------------|
| Windows Super-User identity | Starts as text super-helper; system actions come later |
| Offline-first | 100% local inference |
| Invisible by default | Tray + Orb only |
| Hotkey + Clipboard primary | Core interaction |
| Hardware honesty | Q3/Q4 models + load/unload discipline |
| Safety first | No mutating actions → lowest risk possible |
| Progressive stages | Delivers real daily value before complexity |

Phase 1 is the **interaction kernel**. Stages 2–5 add power and memory around this kernel.

### 3.1 Locked Stage 1 First Principles (`S1-FP-001`)

This plan implements the locked decision recorded in `docs/MASTER_BLUEPRINT.md`:

1. The assistant runs only after explicit hotkey invocation.
2. Inference is local or the request fails with a clear, actionable message.
3. The product is read-only and side-effect free; Copy is explicit and there
   is no automatic paste-back.
4. Clipboard text is the reliable baseline; direct selection capture is
   best-effort.
5. Responsiveness and bounded resource use outrank larger-model intelligence.
6. Only one request may be active. Repeated activation while busy produces
   "already processing" feedback rather than queueing or replacement.
7. UI callbacks never perform clipboard processing, model work, or inference.
8. Input and output content remain ephemeral and are not logged by default.
9. Resource targets and input/output bounds are accepted from measurements on
   target hardware.
10. Expected failures are represented as product states, not crashes.
11. Text capture, orchestration, inference, and presentation remain replaceable
    boundaries.
12. Windows 10/11 evidence is required for technology and acceptance decisions.

These principles are acceptance constraints, not optional implementation
preferences. Changing one requires an explicit update to `S1-FP-001` in the
master blueprint and synchronization of this plan and the knowledge base.

### 3.2 Decision Tracking And Commit Traceability

`docs/DECISION_REGISTER.md` is the canonical ledger for Stage 1 decision
status, approval evidence, verification, and associated implementation
commits. The register does not replace the master blueprint or this plan; it
links stable decision IDs back to their authoritative content.

For every registered decision:

1. Add or update the register entry in the same planning change that changes
   the canonical decision.
2. Include `Decision-ID: <ID>` trailers in implementation, correction,
   rollback, and traceability commits associated with that decision.
3. Record only full commit hashes that already exist. Leave the field `Pending`
   until the implementation commit has been created.
4. Backfill implementation hashes in a follow-up documentation commit because
   a commit cannot contain its own final hash.
5. Append later corrective, completion, or rollback hashes instead of erasing
   earlier history.
6. Mark implementation or verification complete only when the decision's full
   scope or stated evidence is actually complete.

The traceability documentation commit does not record its own hash in the
register; its `Decision-ID:` trailer and Git history provide that link without
creating an infinite self-reference chain.

---

## 4. Success Criteria (Definition of Done)

Phase 1 is considered successful when:

1. User can press a configurable global hotkey from any application.
2. Copied clipboard text is captured reliably; selection capture degrades
   cleanly to the clipboard baseline.
3. A local model produces a response within acceptable time on an 8 GB machine.
4. Response is shown in a compact, non-intrusive floating UI near the Orb/tray.
5. The application can be left running in the tray with low idle resource use.
6. Model can be unloaded to free RAM when not needed.
7. Basic tray menu and minimal settings work.
8. No crashes or freezes under normal use with recommended models.
9. The experience feels clearly different from (and lighter than) opening Jan / AnythingLLM / a full chat window.

---

## 5. Locked Tech Choices For Phase 1

This section implements `S1-STACK-001` from the master blueprint.

| Component | Phase 1 choice | Boundary |
|-----------|----------------|----------|
| Development runtime | Python 3.11+, launched with `python -m oslayer` | Source-mode development and diagnostics |
| Windows release | PyInstaller `--onedir --windowed` | Runnable app, not a Stage 2 installer/updater |
| Inference | Ollama as a separate local service | `http://127.0.0.1:11434`; no cloud fallback |
| HTTP client | One reusable `httpx.AsyncClient` | Explicit connect/read/write/pool timeouts |
| Model | Initial 3B–7B Q4/Q3 candidates | Final list requires 8 GB Windows measurements |
| UI | Tkinter/TTK | One Tk root on the UI thread |
| Tray | `pystray` + Pillow | Windows tray icon, menu, and image assets |
| Clipboard | `pyperclip` plaintext baseline | Selection capture remains an optional adapter |
| Global hotkey | Win32 `RegisterHotKey` through `ctypes` | Dedicated Windows adapter/message handling |
| Single instance | Windows named mutex through `ctypes` | Duplicate launch exits clearly |
| Async/work | `asyncio`, one worker thread, and bounded handoff | One active request; no user-work queue |
| Config | JSON in `%LOCALAPPDATA%/OSLayerAI/` | Safe defaults and recovery from invalid config |
| Diagnostics | Python `logging`, metadata only | No clipboard, prompt, or answer content |

The user-facing package contains the Python interpreter and application
dependencies. Ollama and its models remain separate prerequisites. PyInstaller
must run on Windows to produce the accepted Windows build.

---

## 6. Locked Folder Structure (Phase 1)

```
os-layer-ai/
├── README.md
├── docs/
│   ├── MASTER_BLUEPRINT.md      # strategic reference
│   ├── PHASE1_EXECUTION_PLAN.md # this file
│   └── DECISION_REGISTER.md     # decision/commit traceability
├── pyproject.toml               # package metadata + dependencies
├── assets/
│   └── icon.ico
├── src/oslayer/
│   ├── __init__.py
│   ├── __main__.py              # python -m oslayer
│   ├── main.py                  # composition + lifecycle
│   ├── core/
│   │   ├── contracts.py         # request/result/state/protocol types
│   │   ├── config.py            # JSON settings + validation
│   │   ├── paths.py             # platform-neutral path contract
│   │   ├── prompts.py           # built-in prompt modes
│   │   ├── orchestrator.py      # single-request pipeline
│   │   └── worker.py            # background async worker/handoff
│   ├── inference/
│   │   ├── base.py              # replaceable provider protocol
│   │   ├── ollama_client.py     # direct local HTTP boundary
│   │   └── model_manager.py     # list/status/load/unload
│   ├── platform/
│   │   ├── base.py              # platform adapter contracts
│   │   ├── dev/
│   │   │   ├── hotkey.py        # macOS development trigger
│   │   │   └── paths.py         # development paths
│   │   └── windows/
│   │       ├── hotkey.py        # RegisterHotKey/WM_HOTKEY
│   │       ├── clipboard.py     # Windows clipboard adapter
│   │       ├── single_instance.py
│   │       └── paths.py         # LOCALAPPDATA paths
│   ├── ui/
│   │   ├── app.py               # one Tk root/mainloop
│   │   ├── tray.py
│   │   ├── orb.py
│   │   ├── response_panel.py
│   │   └── settings_panel.py
│   └── utils/
│       └── logging.py
└── tests/
    ├── test_config.py
    ├── test_prompts.py
    ├── test_orchestrator.py
    ├── test_worker.py
    ├── test_ollama_client.py
    ├── test_model_manager.py
    └── test_clipboard.py
```

The `platform/dev` package supports development; it is not a promise of a macOS
product. Windows-only modules are conditionally composed and never imported by
platform-neutral tests on macOS. Later stages can add `actions/`, `memory/`,
and `permissions/` without rewriting the core.

---

## 7. Core Execution Flow (Phase 1)

```
1. Application starts
   → Load config
   → Start system tray
   → Register global hotkey
   → (Optional) Pre-load default model or wait for first use

2. User presses hotkey
   → If a request is active, show "already processing" and stop this activation
   → Capture clipboard text (or best-effort selected text when supported)
   → If empty → show gentle message and exit flow
   → Show Orb / thinking state

3. Orchestrator
   → Build prompt (system instruction + user text)
   → Dispatch through the bounded handoff to the inference worker
   → POST local JSON to `http://127.0.0.1:11434/api/generate`
   → Initially receive one non-streaming response (`stream: false`)
   → Validate the response and map failures to typed product errors

4. UI
   → Display response in compact panel near Orb
   → Offer “Copy” and “Close”
   → Keep only the current result in memory for the active UI session
   → Never paste or replace foreground text automatically

5. Idle behaviour
   → After configurable idle time, unload model to free RAM
   → Tray icon reflects status (ready / thinking / unloaded)
```

### 7.1 Local Ollama Request/Response Contract

Generation sends `model`, separate `system` and `prompt` content, `stream:
false`, a configured `keep_alive`, and bounded generation options to
`/api/generate`. The application consumes the generated `response`, completion
state/reason, and duration/token metadata needed for diagnostics and resource
acceptance. Clipboard and answer content are not written to logs.

Startup/service discovery uses `/api/tags` to list installed models. An idle
unload request uses `keep_alive: 0`. Connection refusal, missing model, timeout,
invalid response, and incomplete generation map to distinct actionable UI
errors. No error path falls back to a remote service.

---

## 8. Detailed Component Responsibilities

### 8.1 Hotkey Module
- Register one configurable global hotkey through Win32 `RegisterHotKey`
- Process `WM_HOTKEY` outside the Tk UI callbacks and unregister cleanly
- Handle conflicts gracefully (warn user, allow change)
- Trigger the main orchestrator flow

### 8.2 Clipboard / Selection Module
- Primary: read plaintext through `pyperclip`
- Secondary: attempt to get current selection (more fragile on Windows)
- Fallback strategy must be clear and documented

### 8.3 Inference Layer
- Use one reusable `httpx.AsyncClient` for the Ollama HTTP API at
  `http://127.0.0.1:11434`
- Support basic generation parameters (temperature, max tokens)
- Detect if Ollama is running; give clear error if not
- Model Manager: list available models, load, unload, report memory impact if possible

### 8.4 Orchestrator
- Single entry point for a request
- Non-blocking (async)
- Enforce one active request and reject repeated activation with clear feedback
- Applies the chosen prompt template (Summarize / Rewrite / Explain / Custom)
- Handles timeouts and errors cleanly

### 8.5 UI Layer
- **App:** one Tkinter root and all widget mutation on its owning thread
- **Tray:** `pystray` icon, tooltip with status, menu (Show, Settings, Quit)
- **Orb:** Tkinter visual indicator that can show thinking / ready states
- **Response Panel:** Tkinter compact panel, always-on-top optional, easy to dismiss, copy button

### 8.6 Config
Persist JSON under `%LOCALAPPDATA%/OSLayerAI/` on Windows. The macOS dev adapter
uses a separate development path.

Minimum settings for Phase 1:
- Hotkey combination
- Default model name
- Default action (summarize / rewrite / etc.)
- Whether to auto-load model on startup
- Idle unload timeout

---

## 9. Prompt Strategy (Phase 1)

Keep prompts simple and focused.

Examples of built-in modes:
- **Summarize** — concise summary
- **Rewrite** — clearer / more professional version
- **Explain** — plain-language explanation
- **Fix Grammar** — correct grammar and spelling only
- **Custom** — user can type a short instruction

System prompt should instruct the model to be concise and directly useful. Avoid long chain-of-thought in the default path.

---

## 10. Dependencies (Locked Baseline)

```
httpx
pystray
Pillow
pyperclip
```

Tkinter/TTK, `asyncio`, `threading`, bounded `queue`, `ctypes`, JSON, logging,
paths, dataclasses, enums, and protocols come from Python/the standard library.
Development dependencies are `pytest`, `pytest-asyncio`, and `ruff` under the
`pyproject.toml` dev extra.

Do not add `keyboard`, `pynput`, CustomTkinter, PySide/PyQt, the Ollama Python
package, `requests`, TOML helpers, Pydantic, `psutil`, or persistence libraries
without evidence that the locked baseline cannot meet a requirement. Pin exact
compatible versions after the first macOS implementation spike and Windows
verification.

---

## 11. Implementation Sequence (Recommended Order)

1. **Project skeleton + config loading**
2. **Ollama client + simple generate test** (CLI first)
3. **Model Manager** (list / load / unload)
4. **Clipboard reader**
5. **Basic orchestrator** (CLI version of the full flow)
6. **System tray**
7. **Global hotkey integration**
8. **Orb + Response Panel**
9. **Wire everything together**
10. **Idle unload + status indicators**
11. **Basic settings UI**
12. **Polish error messages and edge cases**
13. **Windows CI/VM tests + PyInstaller `--onedir --windowed` build**
14. **Interactive Windows integration testing**
15. **Manual acceptance on a real 8 GB Windows machine**

Do not start the floating UI until the backend flow works cleanly from the command line.

---

## 12. Pitfalls to Actively Cover

| Pitfall | How to Handle in Phase 1 |
|---------|---------------------------|
| Hotkey not firing in some apps | Test in browsers, Office, VS Code, Notepad; document limitations |
| Selection vs Clipboard unreliability | Prefer clipboard; make selection an enhancement |
| Ollama not running | Clear tray notification + instructions |
| Model too large → stutter | Enforce recommended model list; unload aggressively |
| UI freezes during inference | Strict async / worker thread separation |
| Focus stealing | Response panel should not aggressively steal keyboard focus |
| Multiple instances | Single-instance check |
| Repeated hotkey while busy | Keep the current request; show “already processing” without queueing |
| High idle RAM | Default to unload after idle timeout |
| Prompt injection from selected text | Treat user text as data; keep system prompt strict |
| Sensitive content in logs/history | Do not persist or content-log input/output by default |

---

## 13. Testing Plan (Phase 1)

**Manual test matrix (minimum):**
- Hotkey works in: Notepad, Chrome, VS Code, Word, File Explorer
- Clipboard empty / very long text / non-text content
- Ollama stopped / model missing / model loading
- Switch models
- Idle unload and reload
- Tray menu actions
- Response panel copy + dismiss
- Low memory simulation (if possible)

**Acceptance test:**  
A non-technical user can install the prerequisites (Ollama + one model), run
the app, copy text, press the hotkey, and receive a useful rewrite/summary
without opening any other window.

---

### 13.1 macOS Development And Windows Verification

This section implements `S1-DEV-001` from the master blueprint.

| Environment | Authorized work | Evidence it cannot provide |
|-------------|-----------------|----------------------------|
| macOS development host | Core code, config, prompts, Ollama HTTP, worker, unit tests, basic Tkinter layout | Win32 hotkey/mutex, Windows focus/tray/DPI behavior, accepted `.exe` |
| Windows CI or VM | Windows imports/tests, PyInstaller build, startup smoke checks | Final interactive UX and target-hardware resource acceptance |
| Interactive Windows 10/11 | Hotkey, clipboard across apps, tray, focus, shutdown, packaged-app behavior | Low-end acceptance unless hardware matches target |
| Real 8 GB target-class Windows PC | Final latency, CPU/RAM, stability, unload/reload acceptance | — |

On macOS, `python -m oslayer --dev` uses a dev trigger in place of the Windows
global hotkey while exercising the real orchestrator, worker, Ollama client,
and result UI. This adapter exists for development only; Stage 1 remains a
Windows product.

PyInstaller is not treated as a cross-compiler. The accepted
`--onedir --windowed` package is built on Windows. Windows CI/VM checks run for each
meaningful integration slice, and physical Windows checks begin with the first
hotkey/tray slice rather than being deferred to release.

---

## 14. Deliverables at End of Phase 1

1. Working application (runnable on Windows)
2. Clear README with setup steps (Ollama + model + run command)
3. Default config and recommended models list
4. This execution plan, Master Blueprint, and Decision Register kept in repo
5. Short demo video or GIF of the core flow (optional but highly recommended)
6. List of known limitations

---

## 15. Exit Criteria → Ready for Stage 2

We move to Stage 2 only when:
- Core hotkey → response loop is stable and pleasant
- Resource behaviour on 8 GB machines is acceptable
- Code structure is clean enough to add permissions and file actions without major rewrite
- We have real usage feedback on the interaction model

---

**End of Phase 1 Execution Plan**

This document is the single source of truth for what “Phase 1 done” means.  
Any feature request that falls outside the In-Scope list should be deferred to Stage 2+ unless it is a critical bug or usability blocker for the core loop.
