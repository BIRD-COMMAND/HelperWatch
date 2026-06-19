# Wearable App

## Overview

The wearable app is an Android/WearOS background service that runs on a commodity smartwatch worn by the child. It is the system's primary sensor platform and the child's direct interface for receiving verbal cues.

## Target Hardware

The app targets any budget Android or WearOS smartwatch that meets these minimum requirements:

- Wi-Fi connectivity
- Bluetooth Low Energy (BLE) for beacon scanning
- Microphone
- Speaker (or Bluetooth audio output for earbuds)
- Accelerometer
- Heart rate sensor (optical PPG)

Most Android-based smartwatches from the last 5 years meet these criteria. Older refurbished devices (e.g., Samsung Galaxy Watch Active2, budget standalone Android watches) are viable and can often be found for $35–80.

See: [Hardware Guide](../guides/Hardware%20Guide.md) for specific device recommendations.

## Core Responsibilities

### BLE Beacon Scanning

The watch continuously scans for Bluetooth Low Energy signals broadcast by room beacons placed throughout the home. It reports the RSSI (signal strength) values for each detected beacon to the local server, which determines the child's current room.

- Scanning runs as a persistent background service.
- Scan interval and power management must balance accuracy with battery life.
- The app does not perform room assignment locally — it sends raw RSSI data to the server for processing.

See: [Indoor Positioning](Indoor%20Positioning.md) for the room-tracking algorithm.

### Audio Capture and Transmission

The watch records audio from the onboard microphone for two purposes:

1. **Continuous ambient monitoring** — Detecting speech, environmental sounds, and vocal patterns indicative of distress.
2. **Triggered capture** — Recording in response to specific events (e.g., a routine step timeout, a biometric spike).

Audio is compressed locally using a low-bitrate codec (e.g., Opus) before transmission to the local server over Wi-Fi. Raw audio is never stored on the watch beyond the active buffer.

### Biometric Sampling

The watch reads heart rate data from the onboard optical sensor at a configurable interval. Heart rate data is sent to the local server as part of the regular telemetry stream. Elevated or rapidly changing heart rate is a key input for the meltdown intercept system.

### Accelerometer Monitoring

The onboard accelerometer provides motion pattern data used to distinguish:

- Walking vs. running (over-the-ground gait analysis)
- Pacing behavior (repetitive movement in a confined area, a common pre-meltdown indicator)
- Stillness (potential task disengagement or distraction)

### Cue Playback

When the local server generates a verbal cue, the watch receives the text or audio payload and plays it:

- **Speaker mode** — Audio plays from the watch's built-in speaker. Suitable for home use where privacy from bystanders is not a concern.
- **Earbud mode** (recommended default) — Audio routes to paired Bluetooth earbuds, keeping the cognitive assistance invisible to others and reducing stigma.

Haptic feedback (vibration patterns) can supplement or replace audio cues for less intrusive prompting.

## Communication

The watch communicates exclusively with the local server hub over the home Wi-Fi network using MQTT or WebSockets. It does not connect to any external servers.

**Telemetry payload** (watch → server):
- Current BLE beacon RSSI values
- Compressed audio stream
- Heart rate reading
- Accelerometer motion classification
- Timestamp

**Command payload** (server → watch):
- Verbal cue text or audio
- Haptic pattern instructions
- Configuration updates (scan interval, audio sensitivity, active routine)

## Battery and Power Considerations

<!-- TODO: Battery life targets and power management strategy will be defined during Phase 3 prototyping. Key trade-offs include BLE scan frequency, audio capture duty cycle, and Wi-Fi transmission interval. -->

## Related Documents

- [System Architecture](System%20Architecture.md) — How the watch fits into the overall system
- [Indoor Positioning](Indoor%20Positioning.md) — BLE beacon scanning and room tracking
- [Hardware Guide](../guides/Hardware%20Guide.md) — Recommended watch models
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Audio data handling and lifecycle
