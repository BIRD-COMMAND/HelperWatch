# Walkthrough: HelperWatch Documentation Pivot & Restructure

## Phase 1: Documentation Restructure

Created 15 focused, cross-linked documents organized into 5 topic areas, distilled from the 478-line discussion log and project brief.

```
docs/
├── project/          Mission and Values, Project Roadmap, Contributing
├── design/           System Architecture, Wearable App, Cloud Backend (replaces Local Server Hub),
│                     Caregiver Mobile App, Indoor Positioning
├── ethics/           Safety and Ethics Overview, Privacy and Data Sovereignty,
│                     Risks and Mitigations
├── guides/           Getting Started, Hardware Guide, FAQ
└── reference/        Glossary
```

---

## Phase 2: Tech Stack Review & Corrections

Conducted an independent review of technology choices named in the initial documentation and applied corrections across 8 files (Kotlin for WearOS, background BLE scanning throttling, mDNS unreliability fallbacks, Ollama lifecycle complexity, Pi 5 latency, battery expectations, etc.).

---

## Phase 3: Architecture Pivot to Cloud-First & ESP32-Scanning

Fully pivoted the documentation set to reflect the Cloud-First and ESP32-scanning model. Purged references to local server hub, Ollama, local benchmarking, mDNS, and Tailscale.

### Key Changes Made

1. **Obsolete Files Deleted:**
   - `docs/design/Local Server Hub.md`
   - `docs/Initial Project Discussion Log.md`

2. **New Cloud Architecture Document Created:**
   - [Cloud Backend.md](file:///c:/dev/HelperWatch/docs/design/Cloud%20Backend.md): Defines telemetry routes (ESP32 RSSI, Watch transcripts), transient processing (on-watch Moonshine STT transcription + transient cloud memory + zero storage), and routine management.

3. **14 Existing Documents Updated:**
   - **Project Brief:** Updates technical philosophy and ethical guardrails to support cloud-first, on-watch STT and ESP32 room scanners.
   - **System Architecture:** Updates diagram, communication paths, and pairing flows (removing mDNS and Tailscale).
   - **Wearable App:** Replaces BLE scanning with BLE advertising. Details on-device transcription (Moonshine STT) and HTTPS/WebSocket connections. Sets battery expectations to a full day (12–16+ hours) due to passive advertising.
   - **Caregiver Mobile App:** Reconnects mobile app directly to the secure Cloud Backend, enabling out-of-the-box remote access without local VPNs.
   - **Indoor Positioning:** Details flipped BLE positioning (watch broadcasts, wall-powered ESP32 nodes scan and measure RSSI). Replaces commercial beacons with ESP32.
   - **Hardware Guide:** Removes local PC/Pi hardware specs, documents Cloud costs, and narrows node hardware to ESP32 dev boards.
   - **Getting Started:** Adapts setup steps to reflect cloud accounts, web-based serial flashing, and device pairing.
   - **FAQ:** Updates general, privacy, hardware, and cost questions to reflect the cloud architecture.
   - **Project Roadmap:** Realigns milestones (ESP32 scanner node firmware, cloud APIs, watch BLE advertiser, on-device STT).
   - **Mission and Values:** Updates Ethical Safety by Design to mention on-device STT and cloud transient processing.
   - **Contributing:** Updates code review guidelines to enforce data minimization.
   - **Glossary:** Removes obsolete terms and introduces Room Scanner Node, Cloud Backend, and Moonshine definitions.
   - **Privacy and Data Sovereignty:** Reframes privacy commitments around on-watch STT, transit encryption, and transient cloud memory with zero persistence.
   - **Risks and Mitigations:** Updates data sovereignty risk and registers two new risks (Internet/Cloud Dependency and Node Deployment/Flashing complexity).
   - **Safety and Ethics Overview:** Updates Guiding Principles to specify data sovereignty via on-device transcription.

4. **All markdown links verified and resolved.**
