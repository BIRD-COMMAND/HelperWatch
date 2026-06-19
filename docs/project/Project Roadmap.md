# Project Roadmap

## Current Status

**Phase 0 — Foundation & Documentation** (active)

The project is in its earliest stage: defining the mission, documenting the architecture, establishing ethical guardrails, and preparing the repository for development.

## Development Phases

### Phase 0: Foundation & Documentation

- Define core mission, values, and ethical framework.
- Document system architecture and component responsibilities.
- Establish the open-source repository structure, licensing, and contribution guidelines.
- Identify target hardware (watches, beacons, server platforms).
- Produce public-facing education materials on safety and privacy.

### Phase 1: Indoor Positioning Prototype

- Set up a test environment with BLE beacons (ESP32 or commercial) in a multi-room space.
- Develop and validate RSSI-based room fingerprinting logic.
- Achieve >90% room-level accuracy in a standard home layout.
- Decide on the communication protocol (MQTT vs. WebSockets) for watch-to-server data.

### Phase 2: Local Server Hub (MVP)

- Package the backend as a desktop application (Electron or Tauri) for Windows and Mac.
- Integrate local speech-to-text (Whisper.cpp or Moonshine).
- Integrate a local language model (via Ollama or equivalent) for generating verbal cues.
- Implement auto-benchmarking to select the appropriate model size for the host hardware.
- Build the cloud API fallback toggle for under-powered hardware.

### Phase 3: Wearable App (MVP)

- Develop the Android/WearOS background service for:
  - Continuous BLE beacon scanning and room reporting.
  - Audio capture, compression, and transmission over local Wi-Fi.
  - Biometric sampling (heart rate) and accelerometer data.
  - Playback of AI-generated verbal cues via speaker or Bluetooth earbuds.
- Pair with the local server hub and validate end-to-end prompting loop.

### Phase 4: Caregiver Mobile App (MVP)

- Build the React Native + Expo mobile app with:
  - Status-at-a-glance home screen (location, activity, biometric state).
  - Push-to-talk intercom mode for parent-to-AI command injection.
  - Macro routine trigger cards (bedtime, leaving the house, cool-down).
- Implement local network discovery via mDNS.

### Phase 5: Behavioral Intelligence

- Implement deterministic guardrails for AI output (parent-approved script routing).
- Build the dynamic fading system to reduce prompts as mastery is demonstrated.
- Develop the micro-affirmation engine for positive reinforcement.
- Add biometric meltdown intercept logic (heart rate spike + accelerometer pacing detection).

### Phase 6: Polish, Testing, & Community Launch

- End-to-end testing with real hardware across multiple home layouts.
- Performance optimization for low-end server hardware (Raspberry Pi 5, budget PCs).
- Finalize caregiver-facing documentation and setup guides.
- Public repository launch with contribution guidelines and ethical manifest.

<!-- TODO: Milestones, target dates, and success criteria will be added as development begins. -->

## Related Documents

- [Mission and Values](Mission%20and%20Values.md) — Why the project exists
- [System Architecture](../design/System%20Architecture.md) — Technical component overview
- [Contributing](Contributing.md) — How to get involved
