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

With the vertical slices validated, build out the full product feature set. Phase 3 is the largest development phase, organized into five workstreams informed by the [Behavioral Use Cases](../design/Behavioral%20Use%20Cases.md) and [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

#### 3A: Child Profile System

Build the profile infrastructure that personalizes the system for each child.

- **Profile data model:** Implement the child profile specification — communication, sensory, executive function, emotional regulation, behavioral, and interaction preference sections.
- **Template profiles:** Pre-built starting configurations for common presentations (non-verbal/severe executive dysfunction, verbal/impulsive/attention-seeking, verbal/anxious/perseverative, limited verbal/sensory-sensitive).
- **Progressive complexity:** Day-one minimum viable profile (name, communication mode, working memory, one routine) with guided expansion over time.
- **Profile management UI:** Caregiver mobile app screens for creating, editing, and managing child profiles.

#### 3B: Response Protocol Engine

Build the context-aware decision system that selects appropriate responses.

- **Protocol structure:** Implement the five-part protocol model (trigger → context qualifier → response action → escalation rules → exit condition).
- **Context evaluation model:** Current state assessment (real-time biometric + behavioral composite), day quality score (rolling daily accumulator), and recent event buffer (short-term memory with time decay).
- **Protocol categories:** Echolalia response, perseveration response, attention/engagement, transition, task re-engagement, routine scaffolding, meltdown arc (build-up/peak/recovery), sensory overload, conflict, and bedtime protocols.
- **Protocol priority and conflict resolution:** 9-level priority hierarchy with suspension/resumption logic.
- **Protocol templates:** Pre-built protocol sets that caregivers can customize.

#### 3C: Behavioral Response Capabilities

Implement the specific features surfaced by the behavioral use cases.

- **Transcript classification:** Echo detection, repetition detection, scripting recognition, avoidance-bid identification, conflict/distress detection.
- **Graduated response strategies:** Multi-phase response logic for perseveration, echolalia, and bedtime resistance.
- **Proactive engagement:** System-initiated check-ins at configurable intervals with contextual follow-up.
- **Transition scaffolding:** Multi-step countdown warnings, bridging cues, first-step anchoring.
- **Task re-engagement:** Room-departure detection during routines, pause/resume state management, re-anchoring cues.
- **Meltdown arc (full cycle):** Rolling-window biometric trend analysis, multi-signal build-up scoring, prompt suppression at peak, recovery detection and buffer, post-meltdown re-engagement.
- **Sensory shutdown detection:** Silence + stillness + non-responsiveness heuristics with automatic prompt suppression.
- **Conflict handling:** Multi-voice distress detection, response suppression, caregiver alert with room context.
- **Bedtime protocols:** Bedtime macro with profile shift, room-exit boundary holding, request classification, graduated firmness, companion/ambient presence mode.
- **Answer consistency enforcement:** No contradictions across graduated response phases.

#### 3D: Caregiver Mobile App Expansion

Expand the mobile app to support the full profile and protocol system.

- **Child profile management:** Profile creation, editing, and template selection with progressive disclosure UX.
- **Routine and protocol configuration:** Routine builder, protocol parameter adjustment, response script customization.
- **Day view / context dashboard:** Real-time visibility into day quality score, recent events, and active protocols.
- **Setup wizard / guided onboarding:** Facilitated or self-guided profile and routine setup workflow.
- **Expanded macro triggers:** Protocol-aware macros with custom profile shifts.
- **Expanded alerts:** Protocol-driven notifications (meltdown build-up, perseveration boundary, shutdown, conflict) with context.
- **Push-to-Talk intercom:** Parent command injection integrated into the AI cue stream.
- **Trend reporting:** Expanded historical data — meltdown frequency and precursors, step-level aversion patterns, perseveration trends, day quality score history.
- **Caregiver log control:** Enable/disable historical logging, on-demand data purging.

#### 3E: Infrastructure

- **Routine engine:** Full routine/schedule management, step-by-step checklists, per-step expected durations, dynamic fading with per-step mastery tracking.
- **Micro-affirmation library:** Varied positive reinforcement cues (not identical each time).
- **ESP32 OTA updates:** Firmware update mechanism via Cloud Backend.
- **Time awareness cues:** Configurable time-context prompts during routines.

**Exit criteria:** A caregiver can set up a child profile (from template or custom), configure routines and protocol parameters, and the system responds to real behavioral scenarios (echolalia, perseveration, transitions, meltdown build-up, task abandonment) with context-appropriate cues — with the caregiver receiving protocol-driven alerts and seeing day-quality context in the app.

### Phase 4: Hardening & Launch

- End-to-end testing with real hardware across multiple home layouts and Wi-Fi conditions.
- Security audit and penetration testing of the Cloud Backend and WebSocket connections.
- WebSocket load testing (concurrent device connections).
- Profile and protocol configuration usability testing with real caregivers.
- Multi-child household testing (sibling dynamics, conflict scenarios).
- Finalize caregiver-facing documentation and setup guides.
- Public repository launch with contribution guidelines and ethical manifest.

## Related Documents

- [Mission and Values](Mission%20and%20Values.md) — Why the project exists
- [System Architecture](../design/System%20Architecture.md) — Technical component overview
- [Behavioral Use Cases](../design/Behavioral%20Use%20Cases.md) — Behavioral scenarios driving Phase 3 features
- [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md) — Child profiles and protocol engine specification
- [Caregiver Mobile App](../design/Caregiver%20Mobile%20App.md) — App design for Phase 3D
- [Contributing](Contributing.md) — How to get involved
