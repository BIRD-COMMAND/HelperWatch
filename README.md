# HelperWatch

HelperWatch is an open-source cognitive scaffolding initiative designed to empower severely impacted special needs children and support their caregivers through accessible, low-cost technology. 

By leveraging commodity smartwatches, simple wall-powered sensor nodes, and a secure Cloud Backend, HelperWatch serves as a **wearable cognitive prosthetic**—providing real-time, room-aware verbal cues, behavioral support, and safety alerts while maintaining a fierce commitment to data privacy.

---

## The Vision: Calm & Autonomous Care

Caregivers of special needs children with severe executive dysfunction or non-verbal autism are often forced into roles of constant prompting, task management, and monitoring. HelperWatch aims to bridge this gap:
- **Empowerment through Independence:** Step-by-step room-aware prompts help the child navigate their daily schedules (e.g. morning routine, mealtimes, bedtime) independently.
- **Caregiver Relief:** Automating the mechanical prompting loop allows parents to focus on emotional support rather than constant task correction.
- **Privacy by Design:** Audio is streamed encrypted to the Cloud Backend for transcription and deleted immediately after — no audio is ever stored at rest.

---

## System Architecture

HelperWatch is built on four clean, interconnected components:

1. **Wearable App (Smartwatch):** A native WearOS background service that acts as the child's interface. It broadcasts a low-power BLE advertising ID, captures audio and streams it encrypted to the Cloud Backend for transcription, monitors biometrics (heart rate and acceleration), and plays back verbal cues.
2. **Room Scanner Nodes:** Small, wall-powered ESP32 microcontroller boards plugged into USB wall chargers in each room. They scan continuously for the smartwatch's BLE signal and report signal strength (RSSI) to the Cloud Backend.
3. **Cloud Backend:** A Node.js/TypeScript server backed by PostgreSQL. It handles speech-to-text transcription (Whisper via Groq), resolves the child's room location from RSSI reports, manages caregiver routines and scripts, runs the LLM classifier/router, and coordinates real-time state sync across all devices.
4. **Caregiver Mobile App:** A React Native + Expo app that provides parents with a status-at-a-glance dashboard (Calm UI), push-to-talk (PTT) intercom overrides, routine configurations, and alerts.

---

## Project Roadmap

HelperWatch is currently in **Phase 0 (Foundation & Documentation)** of development. The high-level milestones include:

- **Phase 0: Foundation & Documentation (Active):** Defining core mission, licensing, architectural standards, and establishing the public repository guidelines.
- **Phase 1: Vertical Slice — Room Detection:** Developing ESP32 scanning firmware, Cloud RSSI endpoint, and a minimal mobile UI to verify >90% room-level accuracy with sub-3 second transitions.
- **Phase 2: Vertical Slice — Prompting Loop:** Building the WearOS background service (BLE advertising, audio streaming, biometric telemetry), Cloud STT integration (Whisper via Groq), LLM classifier/router, and watch cue playback. Target battery life: 12–16+ hours.
- **Phase 3: Core Product Build-Out:** Routine engine, dynamic fading, meltdown intercept, full caregiver mobile app (macros, trend reports, intercom), push notifications, and ESP32 OTA updates.
- **Phase 4: Hardening & Launch:** Security audit, load testing, multi-home testing, documentation finalization, and community launch.

---

## Documentation Index

Explore the HelperWatch wiki directory to learn more:

### 1. Project Overview
- [Mission and Values](docs/project/Mission%20and%20Values.md) — The core values driving HelperWatch.
- [Project Roadmap](docs/project/Project%20Roadmap.md) — Development timeline and current phase.
- [Contributing Guidelines](docs/project/Contributing.md) — How to join the project and submit code.

### 2. Design & Engineering Documents
- [System Architecture](docs/design/System%20Architecture.md) — Component interactions, data flows, and pairing models.
- [Cloud Backend Design](docs/design/Cloud%20Backend.md) — Cloud services, tech stack, APIs, and transient data pipelines.
- [Wearable App Design](docs/design/Wearable%20App.md) — WearOS background service, BLE advertising, and audio streaming.
- [Caregiver Mobile App Design](docs/design/Caregiver%20Mobile%20App.md) — User interface design patterns (Calm UI) and features.
- [Indoor Positioning Logic](docs/design/Indoor%20Positioning.md) — RSSI room resolution algorithm and scanner placement guidelines.

### 3. Safety, Privacy & Ethics
- [Safety and Ethics Overview](docs/ethics/Safety%20and%20Ethics%20Overview.md) — Ethical framework of the project.
- [Privacy and Data Sovereignty](docs/ethics/Privacy%20and%20Data%20Sovereignty.md) — Data protection pledge, transient processing, and caregiver data sovereignty.
- [Risks and Mitigations](docs/ethics/Risks%20and%20Mitigations.md) — Mitigations for AI hallucinations, internet dependency, and deployment difficulty.

### 4. Onboarding Guides & Reference
- [Getting Started Guide](docs/guides/Getting%20Started.md) — Setting up the cloud account, flashing ESP32s, and device pairing.
- [Hardware Guide](docs/guides/Hardware%20Guide.md) — Smartwatch recommendations, ESP32 purchasing, and cloud cost breakdowns.
- [FAQ](docs/guides/FAQ.md) — Frequently asked questions about pricing, setup, and features.
- [Glossary](docs/reference/Glossary.md) — Definitions of terms used across the codebase.