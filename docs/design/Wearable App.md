# Wearable App

## Overview

The wearable app is a native Kotlin/Jetpack Compose for Wear OS background service that runs on a commodity smartwatch worn by the child. It acts as the primary sensor platform and the child's direct interface for receiving verbal cues. Under the cloud-first model, the watch performs on-device speech-to-text (STT) and BLE advertising, offloading scanning and prompt orchestration to the ESP32 network and Cloud Backend.

> **Implementation note:** React Native does not have official support for WearOS. The watch app must be built natively in Kotlin using Jetpack Compose for Wear OS. Logic-layer code (data models, protocol handling) may be shared with the caregiver mobile app via Kotlin Multiplatform (KMP), but the UI and sensor-access layers are entirely separate.

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

Instead of scanning for room beacons (which WearOS aggressively throttles), the watch operates in **BLE advertising mode**.

- The watch background service continuously advertises a unique, secure Bluetooth Low Energy identifier.
- WearOS natively supports persistent BLE advertising with minimal power consumption, and the operating system does not throttle advertising in the background like it does scanning.
- This design completely eliminates background scan timeouts, silent background thread deaths, and the associated room transition latency.

### Audio Capture and On-device STT

To enforce strict data privacy, speech-to-text transcription occurs **directly on the watch**:

- The watch records spoken audio via the onboard microphone.
- An embedded, lightweight STT engine (such as Moonshine STT) transcribes the captured audio to text in real-time.
- **Raw audio is deleted immediately from memory after transcription.** Raw audio files never leave the watch and are never transmitted over the network.
- The resulting text transcript is encrypted and sent to the Cloud Backend.

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
- Text transcript of child's speech
- Heart rate reading
- Accelerometer motion classification
- Timestamp

**Command payload** (Cloud Backend → watch):
- Verbal cue text
- Haptic pattern instructions
- Configuration updates (audio sensitivity, active routine)

## Battery and Power Considerations

By flipping the positioning model from active scanning to passive BLE advertising, the wearable's battery life is significantly improved. Smartwatches with 300–400mAh batteries can expect a **12–16+ hour battery life** under normal caregiving conditions—eliminating the strict requirement for mid-day charging.

**Key optimizations:**
- **BLE Advertising:** BLE advertising has a negligible power footprint compared to continuous or intermittent BLE scanning.
- **On-device STT Duty Cycle:** The STT engine is kept asleep and is only triggered when voice activity detection (VAD) detects speech, or when a routine checklist step is active.
- **Wi-Fi telemetry batching:** Biometric and accelerometer telemetry is batched and transmitted every 5–10 seconds unless a text transcript or critical event needs immediate delivery.

## Related Documents

- [System Architecture](System%20Architecture.md) — How the watch fits into the overall system
- [Cloud Backend](Cloud%20Backend.md) — The cloud services handling telemetry and scripts
- [Indoor Positioning](Indoor%20Positioning.md) — ESP32 room scanning logic
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — On-watch data handling rules

