# Project Roadmap

## Current Status

**Phase 0 — Foundation & Documentation** (active)

The project is in its earliest stage: defining the mission, documenting the architecture, establishing ethical guardrails, and preparing the repository for development.

## Development Phases

### Phase 0: Foundation & Documentation

- Define core mission, values, and ethical framework.
- Document system architecture and component responsibilities.
- Establish the open-source repository structure, licensing, and contribution guidelines.
- Identify target hardware (smartwatches, ESP32 development boards).
- Produce public-facing education materials on safety and privacy.

### Phase 1: Room Positioning Prototype

- Set up a test environment with wall-powered ESP32 scanner nodes and a smartwatch BLE advertising test script.
- Develop ESP32 scanner node firmware to scan for the watch and connect to Wi-Fi.
- Develop and validate RSSI-based room resolution logic on a prototype backend.
- Achieve >90% room-level accuracy in a standard home layout with sub-3 second transition latency.

### Phase 2: Cloud Backend (MVP)

- Set up secure cloud hosting, database schemas, and API routers.
- Implement WebSocket (WSS) and HTTPS endpoints for watch, mobile, and ESP32 telemetry.
- Integrate Cloud LLM APIs (e.g. Groq, OpenAI) for selecting verbal cues.
- Build device provisioning, pairing codes, and caregiver account management APIs.
- Establish transient data pipeline: transient in-memory LLM context, automated logs cleanup, database encryption at rest.

### Phase 3: Wearable App (MVP)

- Develop the native Kotlin/Jetpack Compose for Wear OS background service for:
  - Native, low-power BLE advertising (broadcasting BLE packets continuously).
  - On-device speech-to-text (STT) using Moonshine STT (voice activity detection, local transcription).
  - Telemetry capture (heart rate, accelerometer) and transmission to Cloud Backend over Wi-Fi.
  - Playback of verbal cues.
- Validate battery life under real usage conditions (target: 12-16+ hours of continuous usage).
- Pair with the Cloud Backend and validate end-to-end prompting loop.

### Phase 4: Caregiver Mobile App (MVP)

- Build the React Native + Expo mobile app with:
  - Status-at-a-glance dashboard (location, active routine, biometric state).
  - Push-to-talk intercom mode for parent-to-AI command injection.
  - Macro routine triggers (bedtime, leaving the house, cool-down).
  - Cloud pairing flows for ESP32 nodes and WearOS smartwatch.

### Phase 5: Behavioral Intelligence

- Implement deterministic guardrails for AI output (parent-approved script selection).
- Build the dynamic fading system to reduce prompts as mastery is demonstrated.
- Develop the micro-affirmation engine for positive reinforcement.
- Add biometric meltdown intercept logic (heart rate spike + accelerometer pacing detection).

### Phase 6: Polish, Testing, & Community Launch

- End-to-end testing with real hardware across multiple home layouts and Wi-Fi conditions.
- Perform security audits, penetration testing of the Cloud Backend, and WebSocket load testing.
- Finalize caregiver-facing documentation and setup guides.
- Public repository launch with contribution guidelines and ethical manifest.

## Related Documents

- [Mission and Values](Mission%20and%20Values.md) — Why the project exists
- [System Architecture](../design/System%20Architecture.md) — Technical component overview
- [Contributing](Contributing.md) — How to get involved
