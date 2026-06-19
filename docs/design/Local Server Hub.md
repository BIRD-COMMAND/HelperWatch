# Local Server Hub

## Overview

The local server hub is a desktop application that runs on the family's existing home computer. It is the computational engine of the HelperWatch system — receiving sensor data from the watch, running AI models, generating verbal cues, and serving the caregiver mobile app. It is designed as a "zero-configuration" experience: the caregiver downloads an installer, launches the app, and pairs with the watch.

## Packaging and Distribution

The backend is packaged as a standard desktop application using **Electron** or **Tauri**, distributed as:

- `HelperWatch-Setup.exe` for Windows
- `HelperWatch.dmg` for macOS

The application bundles all dependencies internally. The caregiver never interacts with a terminal, installs Python, or configures a server.

### First-Run Experience

1. The caregiver launches the app.
2. A graphical window displays a **"Scan for Watch"** button.
3. The app auto-detects the home Wi-Fi network and displays a 6-digit pairing code.
4. A visual checklist shows detected room beacons and prompts the caregiver to assign room names.
5. The app runs an automatic hardware benchmark and downloads the appropriate AI model.

## Core Functions

### Speech-to-Text (Local)

Incoming audio from the watch is transcribed using an embedded, local speech-to-text engine:

- **Whisper.cpp** — A C/C++ implementation of OpenAI's Whisper model. Compiles to a single executable with no external dependencies.
- **Moonshine** — An alternative lightweight STT engine optimized for real-time transcription on consumer CPUs.

No audio data is sent to any external service. Raw audio is deleted from memory immediately after transcription.

### AI Orchestration (Local LLM)

The server runs a local language model as a hidden background service for generating contextual verbal cues. Integration options include:

- **Ollama** — Manages model downloads and inference with a simple API.
- **LocalAI** — Alternative local inference server.

The LLM receives structured input (transcript, room, biometrics, time, active routine) and selects or generates a response within deterministic guardrails (see below).

### Auto-Benchmarking

During installation, the app benchmarks the host machine and automatically selects the right model size:

| Hardware Tier | STT Performance | LLM Size | User Experience |
|---------------|----------------|----------|-----------------|
| **Modern Mac** (M1–M4 Apple Silicon) | Real-time | 8B parameters (Llama-3, Qwen3-8B) | Flawless. Sub-second cue generation. |
| **Standard Windows/Linux PC** (Intel i5/i7, AMD Ryzen, no GPU) | Near real-time | 3B parameters (Phi-3, Qwen3-3B) | Good. Adequate for all prompting tasks. |
| **Budget PC or Raspberry Pi 5** | ~1–2 second delay | 1.5B parameters | Functional. Suitable for asynchronous routines. |

The caregiver never sees model names, parameter counts, or quantization details.

### Deterministic Guardrails

The AI does not have free-reign creative freedom. Instead, the LLM acts as a **router** that selects from a strict, parent-approved script of pre-written commands or passes through highly constrained, predictable syntax. This prevents:

- Hallucinated or factually incorrect instructions.
- Inappropriate, confusing, or contradictory cues.
- Unexpected language that could overwhelm a literal-minded child.

See: [Risks and Mitigations](../ethics/Risks%20and%20Mitigations.md) for the hallucination risk discussion.

### Routine Management

The server stores caregiver-configured routines, including:

- Step-by-step task sequences (e.g., morning routine, bedtime routine).
- Room-triggered behaviors (e.g., entering the bathroom starts the hygiene sequence).
- Time-based schedules.
- Meltdown intercept protocols (biometric thresholds, calming scripts).

### Dynamic Fading

The system tracks task completion success over time and automatically reduces prompting frequency as the child demonstrates mastery. If a child successfully navigates the bathroom routine without reminders for a configurable period, the system pulls back, offering fewer prompts and testing autonomy. It re-engages only when failure states or extreme delays are detected.

### Local Data Logging

The server maintains local-only logs of:

- Room transitions and timestamps.
- Task completion times and success rates.
- Biometric trends (heart rate patterns correlated with activities and locations).
- AI cue history.

These logs enable trend reporting in the caregiver mobile app and can produce anonymized summaries for clinical use, but never leave the local machine without explicit caregiver action.

## Cloud API Fallback

For families whose hardware cannot run local models, the app offers a settings toggle:

- **Local Processing** (default) — Everything on the home machine. Free. 100% private.
- **Private Cloud API** — The caregiver enters an API key from a provider (e.g., OpenAI, Groq). Data is anonymized and stripped of metadata before transmission. This is a transparent, educated choice made by the caregiver.

## Network Interface

- **Watch communication** — MQTT or WebSockets over local Wi-Fi.
- **Mobile app communication** — WebSockets over local Wi-Fi, discovered via mDNS (`helperwatch.local`).
- **Remote access** — Optional Tailscale/WireGuard integration for secure access outside the home network.

## Related Documents

- [System Architecture](System%20Architecture.md) — High-level component overview
- [Caregiver Mobile App](Caregiver%20Mobile%20App.md) — The caregiver's frontend
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Data lifecycle and local processing mandate
- [Hardware Guide](../guides/Hardware%20Guide.md) — Server hardware requirements by tier
