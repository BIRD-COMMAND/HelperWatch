# System Architecture

## Overview

HelperWatch is a cloud-coordinated cognitive scaffolding system that connects smartwatches, ESP32 room scanner nodes, and caregiver mobile apps over secure internet protocols to a centralized Cloud Backend.

```
                     ┌───────────────────────┐
                     │     Cloud Backend     │
                     │ (APIs, STT Routing,   │
                     │  LLM Orchestration)   │
                     └───────────┬───────────┘
            ┌────────────────────┼───────────────────┐
            ▼                    ▼                   ▼
     ┌──────────────┐     ┌─────────────┐     ┌──────────────┐
     │  Smartwatch  │     │ ESP32 Room  │     │Mobile App /  │
     │  (BLE adv,   │     │  Scanners   │     │Caregiver UI  │
     │  On-Watch    │     │ (BLE Scan + │     │(React Native)│
     │  Moonshine)  │     │   Wi-Fi)    │     └──────────────┘
     └──────────────┘     └─────────────┘
```

## Components

### Wearable App (Smartwatch)

The child wears a commodity Android or WearOS smartwatch running a native Kotlin background service (Jetpack Compose for Wear OS). The watch is responsible for:

- **BLE advertising** — Continuously broadcasting a unique Bluetooth Low Energy identifier. This is a low-power, native operation that WearOS does not throttle, ensuring stable room detection.
- **On-device speech-to-text** — Transcribing spoken audio locally on the watch using an embedded lightweight model (Moonshine STT). This ensures raw audio never leaves the smartwatch.
- **Biometric sampling** — Reading heart rate data from the onboard optical sensor.
- **Accelerometer monitoring** — Detecting motion patterns (walking, pacing, stillness).
- **Telemetry transmission** — Transmitting text transcripts, biometrics, and accelerometer states to the Cloud Backend over Wi-Fi.
- **Cue playback** — Playing verbal prompts received from the Cloud Backend through the watch speaker or paired Bluetooth earbuds.

See: [Wearable App](Wearable%20App.md)

### Cloud Backend

A secure, managed cloud server that coordinates the HelperWatch system. It handles:

- **Telemetry reception** — Receiving real-time RSSI signal strengths from ESP32 room scanners and biometric/transcript telemetry from the watch.
- **Room resolution** — Determining the child's room location by comparing RSSI signal strength reports from all active room nodes.
- **AI orchestration** — Processing text transcripts, biometrics, and room context through a managed Large Language Model (LLM classifier/router) to select parent-approved verbal cues.
- **Routine management** — Storing caregiver-configured routines, schedules, and scripts.
- **Transient logging** — Storing encrypted trend logs for caregiver review (if logging is enabled) or purging transcripts immediately from memory after processing.

See: [Cloud Backend](Cloud%20Backend.md)

### Caregiver Mobile App

A React Native + Expo mobile application for Android and iOS. This is the caregiver's primary interface to the system. It connects directly to the Cloud Backend and provides:

- **Real-time status dashboard** — Child's current room, active routine, biometric state, and active cue feedback.
- **Push-to-talk intercom** — Parents speak a command that the Cloud Backend integrates into the next verbal cue played to the child.
- **Routine triggers & configuration** — Creating routines, defining scripts, setting biometric thresholds, and triggering macros (e.g., bedtime sequence).
- **Trend reporting** — Visualizing historical data synced from the Cloud Backend (if logging is enabled).

See: [Caregiver Mobile App](Caregiver%20Mobile%20App.md)

### Room Scanner Nodes

Small, wall-powered ESP32 development boards (~$3–4 each) plugged into USB outlets in each room. They are flashed with firmware that:

- Continuously scans for the smartwatch's BLE advertisements.
- Measures the RSSI (signal strength) of the watch's signal.
- Reports the RSSI values to the Cloud Backend over Wi-Fi in real-time.

See: [Indoor Positioning](Indoor%20Positioning.md)

## Communication Protocols

| Path | Protocol | Notes |
|------|----------|-------|
| Watch → Cloud Backend | WebSocket (WSS) | Persistent, bidirectional telemetry and cue delivery |
| ESP32 Nodes → Cloud Backend | WebSocket (WSS) or HTTPS POST | Low-latency RSSI reporting |
| Mobile App → Cloud Backend | WebSocket (WSS) and HTTPS | Real-time state syncing and routine configuration |

## Device Provisioning and Account Association

HelperWatch eliminates the complexity of local discovery protocols (like mDNS) and remote network tunnels (like Tailscale). Devices communicate with a known cloud address.

- **Account Setup:** The caregiver registers a HelperWatch account via the mobile app.
- **ESP32 Node Pairing:** ESP32 room scanners are flashed with the family's Wi-Fi details and an account token via a web-based flashing tool (using WebUSB/WebSerial).
- **Watch Pairing:** The watch app displays a 6-digit pairing code on its first launch, which the caregiver enters into the mobile app to link the wearable to their cloud account.

## Data Flow: End-to-End Prompting Loop

1. The watch broadcasts its unique BLE advertiser ID.
2. The ESP32 scanner node in the kitchen detects the watch with a strong RSSI and reports this to the Cloud Backend.
3. The Cloud Backend resolves that the child has transitioned into the kitchen.
4. The watch captures spoken audio and transcribes it on-device (Moonshine STT).
5. The watch transmits the text transcript + biometrics to the Cloud Backend.
6. The Cloud Backend LLM classifier processes the transcript, active room (kitchen), biometrics, and routine context.
7. The Cloud Backend selects the corresponding parent-approved prompt script (deterministic guardrails).
8. The Cloud Backend sends the prompt text back to the watch over WSS.
9. The watch plays the prompt.
10. The Cloud Backend updates the caregiver mobile app in real-time.

## Related Documents

- [Wearable App](Wearable%20App.md) — Watch-side details
- [Cloud Backend](Cloud%20Backend.md) — Cloud services details
- [Caregiver Mobile App](Caregiver%20Mobile%20App.md) — Caregiver app details
- [Indoor Positioning](Indoor%20Positioning.md) — ESP32 room scanner setup and positioning logic
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Data handling and cloud privacy pledge
