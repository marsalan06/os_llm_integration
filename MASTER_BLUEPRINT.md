# OS-Layer AI — Master Blueprint
**Project Codename:** OS-Layer AI (Windows Super-User Assistant)  
**Document Type:** Master Architecture & Decision Snapshot  
**Last Updated:** 19 November 2026  
**Status:** Pre-implementation locked reference

---

## 1. Vision & Goal

Build a **completely private, system-wide AI utility** that runs fully offline on a “common man computer” (Intel Core i5, 4–8 GB RAM, integrated Intel GPU).

**Primary identity:**  
A **Windows Super-User Assistant** — not another coding agent, not another chat window.

**Desired feeling:**  
Almost invisible. Lives in the system tray. Appears as a small Orb when needed. Activated by global hotkey. Helps with text, later with system actions, always under explicit permission.

**Core promise:**
- No subscription
- No cloud dependency for reasoning
- Works on modest hardware without freezing the machine
- Privacy by default
- Feels native to Windows

---

## 2. Pain Points We Are Solving

| Pain Point | Current Reality | Our Solution Direction |
|------------|-----------------|------------------------|
| Subscription & cloud lock-in | Most useful AI requires monthly fees or API keys | Fully local inference |
| High resource usage | Many local tools stutter or freeze on 8 GB RAM | Strict Q3/Q4 GGUF + careful model lifecycle |
| “Another ChatGPT window” | Most tools force a full chat UI | Tray + Orb + hotkey-first interaction |
| Weak Windows integration | Local AI tools rarely feel system-native | Global hotkey, clipboard, later PowerShell/file actions |
| Dangerous agents | Many agent projects can mutate the system with little control | Deny-by-default permission system + action log |
| No “select text → instant help” flow | Users still copy → open app → paste → wait | Hotkey on selected/clipboard text → response near Orb |
| Complex setup | Heavy installers and configuration | Lightweight, progressive stages |

---

## 3. Competitive Landscape (Deep Snapshot)

### Major Players

| Name | Approx. Stars (Nov 2026) | Core Tech | Primary Focus | Pros | Cons | Relevance |
|------|---------------------------|-----------|---------------|------|------|---------|
| **Ollama** | ~187k | Go + llama.cpp | Local model runner + API | Simple, reliable, huge model library, lightweight service | No UI, no system integration | Preferred inference backend |
| **AnythingLLM** | ~68k | JS/TS + vector DBs | Private document workspace + agents | Strong RAG, workspaces, citations, desktop app | Full app feel, heavier | Reference for later memory stages |
| **Jan** | ~45.7k | Tauri (Rust + web) | Offline ChatGPT alternative | Fully open source, clean UI, local API | Still a full chat window | Good open-source desktop reference |
| **GPT4All** | ~77k | C++/Qt + llama.cpp | Simple offline chat + LocalDocs | Easy on modest hardware | Less active momentum recently | Low-RAM inspiration |
| **PrivateGPT** | ~57.6k | Python + FastAPI | Private RAG / API layer | Strong privacy + API-first | Not a polished consumer desktop | RAG architecture ideas |
| **LM Studio** | High (proprietary) | llama.cpp + MLX | Model browser + local server | Excellent UX for model management | Closed-source main app | Model management UX reference |

### Closer System-Level / Tray Projects (High Relevance)

| Name | Maturity | Key Idea | Notes |
|------|----------|----------|-------|
| **Hotkeys (sprawf)** | Early but focused | Select text → hotkey → AI refine + paste back | Extremely close to our Stage 1 interaction model |
| **Sirius AI Tray** | Early | Tray + clipboard + screenshots + local backends | Practical tray design |
| **TrayMinion** | Early | Tray + on-device Windows AI (Phi Silica) | Native Windows AI path on Copilot+ PCs |
| **AiWinAssistant / WinAssistant** | Early | Offline WPF assistant that can launch apps & search files | Native Windows UI + system control direction |
| **FullyLocal Desktop Companion** | Early | Screen-aware companion via Ollama | Strong privacy + “almost invisible” inspiration |
| **agentic-ai-desktop-suite** | Early | Offline multimodal agent with safety layer | Good safety/allowlist patterns |
| **Alfred, Pyline, ZYRO, VoiceRefine** | Very early | Various tray/hotkey/agent experiments | Useful for patterns, not production maturity |

### Strategic Gap We Target

The market has:
- Excellent **model runners** (Ollama)
- Strong **document/RAG workspaces** (AnythingLLM)
- Decent **full chat desktops** (Jan, GPT4All)

It is still weak on:
- Lightweight, **hotkey-first**, almost-invisible Windows tools
- True **super-user** feel with strong permission gates
- Reliable experience on **4–8 GB RAM** machines without stuttering

**Our positioning:** The quiet, always-available Windows super-user that lives in the tray and only appears when called.

---

## 4. Core Design Principles (Locked Decisions)

1. **Offline-first for reasoning**  
   Main model stays local. Internet tools only when explicitly enabled and needed.

2. **Hardware honesty**  
   Target real common hardware (i5 + 4–8 GB). Quantization is mandatory (Q3/Q4 GGUF).

3. **Invisible by default**  
   System tray + small Orb. No permanent large window.

4. **Hotkey + Clipboard as primary input**  
   Select text or use clipboard → global hotkey → help appears near Orb.

5. **Windows Super-User, not Coding Agent (primary identity)**  
   Coding features come later and remain secondary.

6. **Safety is highest priority**  
   Every mutating action is permission-gated and logged. Deny-by-default.

7. **Progressive stages**  
   Ship useful value early. Do not build full agent before the interaction loop feels good.

8. **Simple, deliberate tech stack**  
   Prefer maintainable and lightweight over trendy complexity.

### 4.1 Stage 1 First Principles (Locked Decision `S1-FP-001`)

**Status:** Locked  
**Approved:** 19 November 2026, by the project owner in planning discussion  
**Implementation:** Pending  
**Verification:** Pending implementation and real Windows 10/11 testing

These principles govern every Stage 1 product, architecture, dependency, and
acceptance decision:

1. **Explicit invocation only**  
   The assistant acts only after a user hotkey. There is no continuous screen,
   clipboard, microphone, or background-content monitoring.

2. **Local or fail clearly**  
   Phase 1 reasoning stays on the machine. If the local backend or model is
   unavailable, show an actionable error; never silently use cloud inference.

3. **Read-only and side-effect free**  
   Phase 1 accepts text and returns text. It does not access files, run commands,
   control applications, or automatically paste/replace content. The user may
   explicitly copy the result.

4. **Clipboard is the reliable input contract**  
   Copied clipboard text is the guaranteed baseline. Direct selected-text
   capture is a best-effort enhancement and cannot be required for Phase 1 to
   work reliably.

5. **Responsiveness over intelligence**  
   Desktop responsiveness, non-blocking feedback, and acceptable latency take
   priority over using a larger model or maximizing answer quality.

6. **One active request, one result**  
   Phase 1 runs at most one inference request at a time. A repeated hotkey while
   busy does not queue or replace work; it shows clear "already processing"
   feedback.

7. **The UI thread never performs model work**  
   Clipboard processing, model lifecycle work, and inference run behind a
   worker/async boundary so the Orb, panel, and Quit action remain responsive.

8. **User content is ephemeral**  
   Clipboard input and generated output are not persisted or content-logged by
   default. At most, the current result may remain in memory for the active UI
   session.

9. **Resource use has an explicit budget**  
   Idle and inference CPU/RAM, latency, input/output bounds, and unload behavior
   must be measured and accepted on target hardware. Exact thresholds are set
   from real-device evidence, not guesses.

10. **Failure is a designed product state**  
    Empty/non-text input, unavailable Ollama, missing models, timeouts, invalid
    config, hotkey conflicts, and duplicate instances produce concise,
    actionable states rather than crashes or silent failure.

11. **Core boundaries remain replaceable**  
    Text capture, orchestration, inference, and presentation are separate
    contracts. Ollama and the selected UI/hotkey libraries are adapters, not
    places to embed core product behavior.

12. **Real Windows evidence decides technology**  
    Major dependency and acceptance decisions require Windows 10/11 evidence,
    including non-admin behavior, event-loop compatibility, footprint,
    packaging behavior, and clean shutdown. Non-Windows tests are supportive,
    not sufficient.

Rejected Stage 1 alternatives include ambient monitoring, required universal
selection capture, silent cloud fallback, automatic paste-back, concurrent or
queued inference, and persistent prompt/response history.

---

## 5. High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     User Interaction Layer                   │
│  Global Hotkey  │  System Tray  │  Orb UI  │  Clipboard     │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                    Orchestrator (Async)                      │
│  - Single-request state machine                              │
│  - Context assembly                                          │
│  - Permission checks                                         │
│  - Response routing                                          │
└────────────┬───────────────────────────────┬────────────────┘
             │                               │
             ▼                               ▼
┌────────────────────────┐      ┌────────────────────────────┐
│   Inference Worker     │      │   Future Action Workers    │
│   (Ollama local HTTP)  │      │   (File, PowerShell, etc.) │
│   Q3/Q4 GGUF models    │      │   (Stage 2+)               │
└────────────────────────┘      └────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│                 Persistence & Memory (later)                 │
│  Action Log │ Settings │ Vector DB │ Knowledge Graph        │
└─────────────────────────────────────────────────────────────┘
```

**Key architectural rules:**
- Orchestrator never blocks the UI thread.
- Model can be loaded/unloaded based on memory pressure.
- All side-effect actions go through a permission gate.
- Idle-time indexing only (never while user is actively working).

---

## 6. Stage 1 Runtime And Stack (Locked Decision `S1-STACK-001`)

**Status:** Locked  
**Approved:** 19 November 2026, by the project owner in planning discussion  
**Implementation:** Pending  
**Verification:** Pending implementation, Windows packaging, and target-device tests

Phase 1 runs as Python source during development and ships as a Windows app.
Ollama remains a separately installed local service; it and the selected model
are not bundled into the application.

| Layer | Locked Phase 1 choice | Reason |
|-------|-----------------------|--------|
| Language | Python 3.11+ | Fast iteration and good Windows/library support |
| User release | PyInstaller `--onedir --windowed` Windows app | No user Python requirement; simpler startup and diagnostics than one-file packaging |
| Inference service | Separately installed Ollama on `127.0.0.1:11434` | Local-only model lifecycle and HTTP API |
| Inference client | Direct HTTP with one reusable `httpx.AsyncClient` | Explicit contracts, streaming/timeouts, no Ollama SDK coupling |
| Model format | GGUF Q4 / Q3 through Ollama | Better fit for the target 4–8 GB class than larger models |
| UI | Standard-library Tkinter/TTK | Minimal dependency and adequate compact desktop UI |
| Tray | `pystray` + Pillow | Tray lifecycle/menu plus icon support |
| Clipboard | `pyperclip` for plaintext baseline | Small, direct Windows clipboard adapter |
| Hotkey | Win32 `RegisterHotKey` through `ctypes` | Native system-wide hotkey, conflict reporting, no keyboard-hook package |
| Single instance | Windows named mutex through `ctypes` | Native duplicate-instance guard |
| Concurrency | Tk main thread + one inference worker + bounded handoff | Responsive UI with no queued user requests |
| Config | JSON under `%LOCALAPPDATA%/OSLayerAI/` | Standard library, readable, simple recovery |
| Logging | Standard-library structured metadata logs | Diagnostics without input/output content persistence |
| Tests/tooling | `pytest`, `pytest-asyncio`, `ruff` | Focused async tests plus lint/format checks |

Initial runtime dependencies are limited to `httpx`, `pystray`, Pillow, and
`pyperclip`. Exact compatible versions will be pinned after the implementation
spike and Windows verification.

**Explicit Phase 1 non-choices:**
- Electron or another web runtime for the core application
- PySide/PyQt or CustomTkinter before standard Tkinter proves insufficient
- `keyboard` or `pynput` instead of the native hotkey API
- The Ollama Python SDK or `requests` in addition to `httpx`
- TOML/config libraries, Pydantic, `psutil`, databases, or vector stores
- Always-on large models or any cloud inference fallback

### 6.1 Development Host And Windows Evidence (`S1-DEV-001`)

**Status:** Locked  
**Approved:** 19 November 2026, by the project owner in planning discussion  
**Implementation:** Pending  
**Verification:** Pending CI/VM and physical Windows execution

Daily development may occur on macOS. Core contracts, configuration, prompts,
orchestration, Ollama HTTP integration, worker behavior, tests, and basic
Tkinter layout remain platform-independent. Windows-specific adapters must be
isolated behind platform contracts and must not be imported on macOS.

The development/verification path is:

1. Build and unit-test the platform-neutral core on macOS.
2. Use a dev trigger/adapter on macOS instead of pretending to validate the
   Windows global hotkey or named mutex.
3. Run Windows automated tests and a PyInstaller build in a Windows CI runner
   or VM for each meaningful integration slice.
4. Test the hotkey, tray, clipboard behavior, focus, DPI/multi-monitor behavior,
   shutdown, and packaged app on interactive Windows 10/11.
5. Perform final latency and CPU/RAM acceptance on a real 8 GB target-class
   Windows machine.

PyInstaller output is built on Windows; the macOS development host cannot
produce the accepted Windows executable. CI/VM results support, but do not
replace, physical Windows acceptance evidence.

---

## 7. Safety & Permissions Model

**Philosophy:** Deny by default. Explicit grant. Full audit.

- **Phase 1:** No mutating actions at all (read-only text help).
- **Later phases:**
  - Permission levels (None / Ask / Allow for this project / Always allow specific action)
  - Project / folder scoping
  - Action log with timestamp, action type, target, result
  - Clear visual confirmation for any write / command execution

Every future PowerShell or file-write capability must pass through this gate.

---

## 8. Interaction Model (UX)

**Primary flow (Phase 1):**
1. User copies text to the clipboard. Direct selected-text capture may be used
   when it works reliably, but it is not the baseline contract.
2. Presses global hotkey.
3. Small Orb appears (or already visible in tray).
4. Local model processes the text (summarize, rewrite, explain, fix, etc.).
5. Response shown near the Orb or in a compact floating panel.
6. User can explicitly copy the result or dismiss it; the app does not
   automatically paste or replace text.

**Tray menu (basic):**
- Open Orb / Status
- Model status (loaded / unloaded)
- Quick settings
- Exit

**Design goals:**
- Sub-second feeling for simple requests where possible
- Never steal focus aggressively
- Clear loading / thinking state
- Easy to dismiss

---

## 9. Phase-Wise Roadmap

### Stage 1 – MVP (Current Focus)
- Global hotkey + clipboard → local model → response near Orb
- Basic system tray
- Model load/unload management
- No file writing, no system actions
- Goal: Prove the core interaction loop feels good and stays light on resources

### Stage 2
- Proper installer
- Settings UI
- Permission system foundation
- Action log
- Simple file read (with permission)

### Stage 3
- Full text-file read/write with diff preview
- Vector DB + basic Knowledge Graph
- Project detection heuristics
- Idle-only indexing

### Stage 4
- Coding-oriented features (multi-file awareness, command running)
- Controlled PowerShell actions
- Optional internet tools (user-gated)

### Stage 5
- Deep Windows integration polish
- Reliability, performance, edge cases
- Broader action set under the same safety model

---

## 10. Key Risks & Pitfalls (Must Cover)

| Risk | Mitigation |
|------|------------|
| Model causes system freeze on low RAM | Strict quantization + unload when idle + memory monitoring |
| Hotkey conflicts / reliability on Windows | Test multiple libraries; provide configurable hotkey |
| UI feels laggy or “heavy” | Keep Orb extremely lightweight; avoid heavy frameworks early |
| Scope creep into full coding agent too early | Explicit Stage 1 non-goals; protect the super-user identity |
| Permission system becomes complex / ignored | Start simple, make every mutation require clear consent |
| Users expect cloud-level quality | Set expectations; focus on usefulness + privacy + speed of access |
| Packaging / distribution pain on Windows | Plan Stage 2 installer carefully; avoid fragile PyInstaller defaults |

---

## 11. Success Metrics (High Level)

**Stage 1 success looks like:**
- Hotkey reliably triggers on Windows 10/11
- Response appears near Orb without opening a full chat window
- Runs without freezing a real 8 GB machine using Q4 models
- Feels faster and more native than opening any existing local chat app
- Users understand it as “my Windows helper” rather than “another AI chatbot”

---

## 12. Document Hierarchy

- **This file (`MASTER_BLUEPRINT.md`)** = Single source of truth for vision, decisions, architecture, competition, and roadmap.
- **`PHASE1_EXECUTION_PLAN.md`** = Detailed, actionable plan for the first release only.
- Future phase documents will reference this master blueprint.

---

**End of Master Blueprint**  
All major decisions above are considered locked unless explicitly revisited.  
Next concrete artifact: Phase 1 Execution Plan.
