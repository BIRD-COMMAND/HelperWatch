# System Architecture

## Overview

HelperWatch is a three-component system connected over a local home Wi-Fi network. No data leaves the home network by default.

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Home Wi-Fi Network                          │
│                                                                     │
│  ┌──────────────────┐    ┌──────────────────┐    ┌────────────────┐ │
│  │   Wearable App   │    │ Local Server Hub │    │ Caregiver App  │ │
│  │  (Android/WearOS │◄──►│ (Desktop App:    │◄──►│ (React Native  │ │
│  │   Smartwatch)    │    │  Windows / Mac)  │    │  Mobile App)   │ │
│  └──────────────────┘    └──────────────────┘    └────────────────┘ │
│          ▲                        ▲                                  │
│          │ BLE                    │ Optional                        │
│  ┌───────┴──────────┐    ┌───────┴──────────┐                      │
│  │  Room Beacons    │    │  Cloud AI API    │                      │
│  │ (ESP32 / BLE     │    │  (Fallback only, │                      │
│  │  Tags per room)  │    │   user opt-in)   │                      │
│  └──────────────────┘    └──────────────────┘                      │
└─────────────────────────────────────────────────────────────────────┘
```

## Components

### Wearable App (Smartwatch)

The child wears a commodity Android or WearOS smartwatch running a background service. The watch is responsible for:

- **BLE beacon scanning** — Continuously scanning for nearby Bluetooth Low Energy signals from room beacons to determine which room the child is in.
- **Audio capture** — Recording ambient and spoken audio via the onboard microphone, compressing it (e.g., Opus codec), and transmitting it to the local server over Wi-Fi.
- **Biometric sampling** — Reading heart rate data from the onboard sensor.
- **Accelerometer monitoring** — Detecting motion patterns (walking, running, pacing, stillness).
- **Cue playback** — Playing AI-generated verbal prompts through the watch speaker or routing audio to paired Bluetooth earbuds.

See: [Wearable App](Wearable%20App.md)

### Local Server Hub (Desktop Application)

A standard desktop application (packaged via Electron or Tauri) that runs on the family's existing home computer. This is the computational engine of the system. It handles:

- **Speech-to-text** — Transcribing incoming audio using an embedded local model (Whisper.cpp or Moonshine). No audio data leaves the machine.
- **AI orchestration** — Processing transcripts, biometric data, and room context through a local language model (via Ollama or equivalent) to generate appropriate verbal cues.
- **Routine management** — Storing parent-configured routines, scripts, and schedules.
- **Data logging** — Maintaining local-only logs of room transitions, task completions, and biometric trends for caregiver review.
- **Auto-benchmarking** — Detecting the host machine's capabilities at install time and selecting the appropriate AI model size automatically.

See: [Local Server Hub](Local%20Server%20Hub.md)

### Caregiver Mobile App

A React Native + Expo mobile application for Android and iOS. This is the caregiver's primary interface to the system. It provides:

- **Real-time status dashboard** — Child's current room, active routine, biometric state, and AI activity at a glance.
- **Push-to-talk intercom** — Parent speaks a command that the AI integrates into its next cue to the child, maintaining the familiar AI voice.
- **Routine triggers** — One-tap macro cards to start bedtime routines, transition sequences, or meltdown intercepts.
- **Trend reporting** — Historical data visualizations for clinical or personal use.

See: [Caregiver Mobile App](Caregiver%20Mobile%20App.md)

### Room Beacons (Indoor Positioning)

Small, inexpensive devices placed in each room that continuously broadcast a unique Bluetooth Low Energy identifier. Two options:

- **ESP32 development boards** (~$3–4 each) plugged into USB wall chargers, flashed with a static beacon script.
- **Commercial BLE beacon tags** (~$10–15 each) running on coin-cell batteries for 1+ years.

The wearable scans for these signals and uses RSSI (signal strength) to determine room-level location with >90% accuracy.

See: [Indoor Positioning](Indoor%20Positioning.md)

## Communication Protocols

| Path | Protocol | Notes |
|------|----------|-------|
| Watch ↔ Server | MQTT or WebSockets over Wi-Fi | Lightweight, low-latency, bidirectional |
| Mobile App ↔ Server | WebSockets over Wi-Fi | Same local network; discovered via mDNS |
| Mobile App ↔ Server (remote) | Tailscale / WireGuard tunnel | Encrypted peer-to-peer; no middleman |
| Server → Cloud API (optional) | HTTPS | User opt-in only; data anonymized before transmission |

## Network Discovery

The system uses **mDNS / Zero-Configuration Networking** for local device discovery. When the mobile app or watch boots, it broadcasts a local query for `helperwatch.local`, similar to how Chromecast or AirPrint devices are discovered. No manual IP configuration is required.

## Data Flow: End-to-End Prompting Loop

1. The watch detects the child has entered the kitchen (BLE beacon signal spike).
2. The watch captures ambient audio and compresses it.
3. Audio + room context + biometric snapshot are sent to the local server over Wi-Fi.
4. The server transcribes the audio locally (Whisper.cpp).
5. The server feeds the transcript, room, biometrics, time of day, and active routine into the local LLM.
6. The LLM selects an appropriate response from the parent-approved script set (deterministic guardrails).
7. The server sends the text cue back to the watch.
8. The watch plays the cue through the speaker or earbuds.
9. The server updates the caregiver mobile app dashboard in real time.

## Cloud API Fallback

For families whose hardware cannot run local AI models (e.g., Chromebooks or very old PCs), the server hub offers a toggle:

- **Local Processing** (default) — Everything stays on the home computer. Free. 100% private.
- **Private Cloud API** — The caregiver inputs an API key (e.g., OpenAI, Groq). Data is anonymized and stripped of metadata before transmission. The caregiver makes this choice transparently, understanding the trade-off.

## Related Documents

- [Wearable App](Wearable%20App.md) — Watch-side technical details
- [Local Server Hub](Local%20Server%20Hub.md) — Backend application details
- [Caregiver Mobile App](Caregiver%20Mobile%20App.md) — Mobile UX and engineering
- [Indoor Positioning](Indoor%20Positioning.md) — BLE beacon setup and room tracking
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Data handling and lifecycle
