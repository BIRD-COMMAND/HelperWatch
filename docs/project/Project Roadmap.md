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

### Phase 1: Vertical Slice — Room Detection

The first end-to-end integration milestone. Build the minimum stack needed to answer: *"What room is the child in?"*

- Develop ESP32 scanner node firmware (BLE scan + Wi-Fi RSSI reporting).
- Stand up a minimal Cloud Backend (Node.js/TypeScript) with the RSSI telemetry endpoint and room resolution logic.
- Build a minimal mobile UI (React Native + Expo) that displays the child's current room name in real-time.
- Implement ESP32 provisioning: WebUSB/WebSerial flashing with Wi-Fi credentials and per-account API key.
- Implement caregiver account creation and ESP32 node registration.
- Validate >90% room-level accuracy in a test home with sub-3 second transition latency.

**Exit criteria:** A caregiver can open the mobile app and see the child's room update in real-time as the child moves through the house.

### Phase 2: Vertical Slice — Prompting Loop

The core product loop, tested end-to-end. Build the minimum stack needed to answer: *"Can the system hear the child, understand their context, and deliver the right prompt?"*

- Develop the WearOS background service (Kotlin/Jetpack Compose):
  - BLE advertising (broadcasting unique identifier).
  - Audio capture and encrypted streaming to Cloud Backend over WSS.
  - Telemetry capture (heart rate, accelerometer) and WSS transmission.
  - Verbal cue playback (speaker and earbud modes).
- Extend the Cloud Backend:
  - Watch telemetry WSS endpoint.
  - Cloud STT integration (Whisper via Groq) for server-side transcription.
  - LLM classifier/router integration (Groq primary, OpenAI fallback).
  - Basic routine storage and prompt selection.
  - Device pairing (6-digit code flow for watch).
- Extend the mobile app:
  - Status-at-a-glance view (room + active routine + last cue).
  - Watch pairing UI.
- Validate battery life under real usage conditions (target: 12–16+ hours).

**Exit criteria:** A caregiver can configure a simple routine, the child speaks, and the watch plays the correct prompt based on room, speech, and routine context — with the caregiver seeing status updates in the mobile app.

### Phase 3: Core Product Build-Out

With the vertical slices validated, build out the full product feature set:

- **Routine Engine:** Full routine/schedule management, step-by-step checklists, macro triggers (bedtime, leaving the house, cool-down).
- **Dynamic Fading:** Reduce prompt frequency as the child demonstrates mastery.
- **Meltdown Intercept:** Heart rate spike + accelerometer pacing detection → calming cue + caregiver push notification.
- **Micro-Affirmations:** Positive reinforcement engine for task step completions.
- **Push-to-Talk Intercom:** Parent speaks a command; AI integrates it into the next cue.
- **Trend Reporting:** Historical data visualization (room timelines, task durations, biometric patterns).
- **ESP32 OTA Updates:** Firmware update mechanism via Cloud Backend.
- **Caregiver Log Control:** Enable/disable historical logging, on-demand data purging.

### Phase 4: Hardening & Launch

- End-to-end testing with real hardware across multiple home layouts and Wi-Fi conditions.
- Security audit and penetration testing of the Cloud Backend and WebSocket connections.
- WebSocket load testing (concurrent device connections).
- Finalize caregiver-facing documentation and setup guides.
- Public repository launch with contribution guidelines and ethical manifest.

## Related Documents

- [Mission and Values](Mission%20and%20Values.md) — Why the project exists
- [System Architecture](../design/System%20Architecture.md) — Technical component overview
- [Contributing](Contributing.md) — How to get involved
