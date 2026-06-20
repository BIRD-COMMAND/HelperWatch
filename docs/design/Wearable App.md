# Wearable App

## Overview

The wearable app is a native Kotlin/Jetpack Compose for Wear OS background service that runs on a commodity smartwatch worn by the child. It acts as the primary sensor platform and the child's direct interface for receiving verbal cues. Under the cloud-first model, the watch captures audio, streams it encrypted to the Cloud Backend for transcription, and performs BLE advertising — offloading scanning, STT, and prompt orchestration to the ESP32 network and Cloud Backend.

> **Implementation note:** React Native does not have official support for WearOS. The watch app is built natively in Kotlin using Jetpack Compose for Wear OS. The watch app and caregiver mobile app are entirely separate codebases that communicate through the shared Cloud Backend API.

## Target Hardware

The app targets any budget Android or WearOS smartwatch that meets these minimum requirements:

- Wi-Fi connectivity
- Bluetooth Low Energy (BLE) support for advertising
- Microphone
- Speaker (or Bluetooth audio output for earbuds)
- Accelerometer
- Heart rate sensor (optical PPG)

Most Android-based smartwatches from the last 5 years meet these criteria. Refurbished devices (e.g., Samsung Galaxy Watch Active2, budget WearOS devices) are viable options.

See: [Hardware Guide](../guides/Hardware%20Guide.md) for specific device recommendations.

## Core Responsibilities

### BLE Advertising

To avoid background scanning limitations and aggressive operating system throttling, the watch operates in **BLE advertising mode**.

- The watch background service continuously advertises a unique, secure Bluetooth Low Energy identifier.
- WearOS natively supports persistent BLE advertising with minimal power consumption, and the operating system does not throttle advertising in the background like it does scanning.
- This design completely eliminates background scan timeouts, silent background thread deaths, and the associated room transition latency.

### Audio Capture and Cloud STT

The watch captures audio and streams it to the Cloud Backend for transcription:

1. The watch records spoken audio via the onboard microphone.
2. Audio is streamed encrypted (TLS) to the Cloud Backend over the persistent WSS connection.
3. The Cloud Backend transcribes the audio using a managed STT API (Whisper via Groq).
4. The resulting text transcript enters the transient processing pipeline for LLM classification.
5. **Raw audio is processed in-memory on the Cloud Backend and deleted immediately after transcription.** Audio is never written to persistent storage.

This approach keeps the watch's role simple (capture and stream) and avoids the significant complexity, battery drain, and hardware compatibility risk of running inference models on budget WearOS devices. Privacy is maintained through encrypted transit, transient in-memory processing, and immediate deletion — the same model used for all other sensitive data in the system.

### Biometric Sampling

The watch reads heart rate data from the onboard optical sensor at a configurable interval. Heart rate data is sent to the Cloud Backend as part of the encrypted telemetry stream. Elevated or rapidly changing heart rate is a key input for detecting emotional dysregulation.

### Accelerometer Monitoring

The onboard accelerometer provides motion pattern data used to distinguish:
- Walking vs. running (over-the-ground gait analysis).
- Pacing behavior (repetitive movement in a confined area, a key pre-meltdown indicator).
- Stillness (potential task disengagement or distraction).

### Cue Playback

When the Cloud Backend generates a verbal cue, the watch receives the text payload and plays it:
- **Speaker mode** — Audio plays from the watch's built-in speaker. Suitable for home use where privacy from bystanders is not a concern.
- **Earbud mode** (recommended default) — Audio routes to paired Bluetooth earbuds, keeping the cognitive assistance invisible to others and reducing social stigma.
- Haptic feedback (vibration patterns) can supplement or replace audio cues for less intrusive prompting.

## Communication

The watch communicates directly with the Cloud Backend over Wi-Fi using a secure WebSocket (WSS) connection.

**Telemetry payload** (watch → Cloud Backend):
- Watch Device ID and session token
- Encrypted audio stream (for cloud STT)
- Heart rate reading
- Accelerometer motion classification
- Timestamp

**Command payload** (Cloud Backend → watch):
- Verbal cue text
- Haptic pattern instructions
- Configuration updates (audio sensitivity, active routine)

## Battery and Power Considerations

By keeping the watch's role to BLE advertising, audio streaming, and sensor telemetry — with no on-device inference — we expect **12–16+ hours of battery life** under typical caregiving conditions.

**Key optimizations:**
- **BLE Advertising:** BLE advertising has a negligible power footprint compared to continuous or intermittent BLE scanning.
- **Audio Duty Cycle:** Audio capture and streaming is gated by voice activity detection (VAD) and only activates when speech is detected or a routine checklist step is active. The microphone and Wi-Fi radio are idle between speech events.
- **Wi-Fi telemetry batching:** Biometric and accelerometer telemetry is batched and transmitted every 5–10 seconds unless a critical event needs immediate delivery.

## Related Documents

- [System Architecture](System%20Architecture.md) — How the watch fits into the overall system
- [Cloud Backend](Cloud%20Backend.md) — The cloud services handling STT, telemetry, and scripts
- [Indoor Positioning](Indoor%20Positioning.md) — ESP32 room scanning logic
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Data handling rules
