# Glossary

Key terms used throughout the HelperWatch documentation.

---

**BLE (Bluetooth Low Energy)**
A low-power wireless communication protocol used by room beacons to broadcast their identity. The smartwatch scans for BLE signals to determine which room the child is in.

**Bystander Privacy**
The concern that a wearable microphone captures audio from people other than the child — siblings, visitors, therapists — without their knowledge or consent. Mitigated through immediate audio deletion, privacy zones, and quick-mute controls.

**Calm UI**
A user interface design philosophy (Weiser & Brown, 1997) that minimizes cognitive load by silently monitoring in the background and only demanding the user's attention when truly necessary. The caregiver mobile app follows this principle.

**Cloud API Fallback**
An optional mode where the local server sends anonymized text (not raw audio) to a cloud AI provider for processing. Used only when the family's hardware cannot run local AI models. Requires explicit caregiver opt-in.

**Cognitive Prosthetic**
The conceptual framing of HelperWatch: a system that acts as an external extension of executive function for children who cannot independently manage sequencing, attention, and self-regulation.

**Cognitive Scaffolding**
Providing structured, step-by-step support to help a person complete tasks they cannot yet manage independently. HelperWatch provides scaffolding through real-time verbal cues, location-aware prompting, and micro-affirmations.

**Deterministic Guardrails**
Architectural constraints that prevent the AI from generating freeform, unpredictable language. The AI acts as a router selecting from parent-approved scripts rather than having creative freedom, preventing hallucinations and unsafe instructions.

**Dynamic Fading**
The system's ability to automatically reduce prompting frequency as the child demonstrates mastery of a routine. Designed to build independence rather than dependency. The ultimate success metric is a child who no longer needs the device.

**ESP32**
A low-cost microcontroller board (~$3–4) used as a BLE room beacon. Powered by USB wall chargers and flashed with a simple beacon script.

**Hallucination (AI)**
When a language model generates plausible-sounding but factually incorrect, illogical, or inappropriate output. A critical risk in the HelperWatch context, mitigated by deterministic guardrails.

**Intercom Mode (Push-to-Talk)**
A caregiver mobile app feature that allows the parent to speak a command that the AI integrates into its next verbal cue to the child, maintaining the familiar AI voice rather than jarring the child with a direct walkie-talkie transmission.

**Local AI Sovereignty**
The principle that all sensitive data (audio, biometrics) must be processed on the family's own hardware within their home network. No raw data leaves the home by default.

**Macro Routine Trigger**
A one-tap card in the caregiver mobile app that switches the AI's entire behavior profile — adjusting prompt frequency, biometric thresholds, and active scripts — to match a specific scenario (bedtime, leaving the house, cool-down).

**mDNS (Multicast DNS)**
A zero-configuration networking protocol that allows devices on the same local network to discover each other by name (e.g., `helperwatch.local`) without manual IP configuration. Used by the mobile app and watch to find the local server hub.

**Meltdown Intercept**
The system's ability to detect early warning signs of emotional dysregulation (biometric spikes, pacing, vocal changes) and intervene with calming cues before a full meltdown manifests.

**Micro-Affirmation**
Brief, immediate positive reinforcement delivered by the AI after the child completes a small step. Designed to shift the background noise of the child's day from constant correction to consistent encouragement.

**MQTT (Message Queuing Telemetry Transport)**
A lightweight messaging protocol designed for constrained devices and low-bandwidth networks. A candidate protocol for communication between the watch and the local server.

**Ollama**
An open-source tool for running large language models locally. Used by the HelperWatch server hub to host the AI model that generates verbal cues.

**Privacy Zone**
A caregiver-configured room or time window where audio capture is automatically paused on the watch, protecting bystander privacy during sensitive situations.

**RSSI (Received Signal Strength Indicator)**
A measurement of how strong a Bluetooth signal is at the receiver. The watch uses RSSI values from multiple room beacons to determine which room the child is in (the beacon with the strongest signal is typically the closest).

**RSSI Fingerprinting**
A technique where the system learns the unique pattern of BLE signal strengths associated with each room during a calibration phase, and then matches incoming signals against those patterns to identify the current room.

**Room Beacon**
A small BLE-broadcasting device placed in each room of the home. Either an ESP32 development board or a commercial BLE tag. The watch scans for these beacons to determine room-level location.

**Tailscale / WireGuard**
Open-source tools that create encrypted, peer-to-peer network tunnels. Used optionally to allow the caregiver mobile app to securely access the home server from outside the home Wi-Fi network, without relying on a corporate cloud intermediary.

**WebSocket**
A communication protocol that provides persistent, bidirectional communication between two endpoints over a single connection. A candidate protocol for real-time data exchange between HelperWatch components.

**Whisper.cpp / Moonshine**
Lightweight, locally-running speech-to-text engines used by the HelperWatch server hub. They transcribe audio on the family's own hardware without sending data to external services.

---

## Related Documents

- [System Architecture](../design/System%20Architecture.md) — Technical overview using these terms
- [Indoor Positioning](../design/Indoor%20Positioning.md) — BLE and RSSI concepts in practice
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Privacy terms in context
