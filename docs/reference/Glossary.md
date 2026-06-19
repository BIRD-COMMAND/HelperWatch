# Glossary

Key terms used throughout the HelperWatch documentation.

---

**BLE (Bluetooth Low Energy)**
A low-power wireless communication protocol. The smartwatch broadcasts BLE signals, and room scanner nodes scan for these signals to determine which room the child is in.

**Bystander Privacy**
The concern that a wearable microphone captures audio from people other than the child — siblings, visitors, therapists — without their knowledge or consent. Mitigated through immediate audio deletion on-device, privacy zones, and quick-mute controls.

**Calm UI**
A user interface design philosophy (Weiser & Brown, 1997) that minimizes cognitive load by silently monitoring in the background and only demanding the user's attention when truly necessary. The caregiver mobile app follows this principle.

**Cloud Backend**
The central coordination engine of the HelperWatch system, hosted securely in the cloud. It manages user accounts, routine configurations, room location resolution, and AI orchestration.

**Cognitive Prosthetic**
The conceptual framing of HelperWatch: a system that acts as an external extension of executive function for children who cannot independently manage sequencing, attention, and self-regulation.

**Cognitive Scaffolding**
Providing structured, step-by-step support to help a person complete tasks they cannot yet manage independently. HelperWatch provides scaffolding through real-time verbal cues, location-aware prompting, and micro-affirmations.

**Data Sovereignty & Minimization**
The principle that the family's sensitive data is owned and controlled by them. In HelperWatch, this is achieved by performing speech-to-text transcription directly on the smartwatch, encrypting all data in transit, and using a Cloud Backend that processes data transiently in memory without permanent storage.

**Deterministic Guardrails**
Architectural constraints that prevent the AI from generating freeform, unpredictable language. The Cloud Backend acts as a router selecting from parent-approved scripts rather than having creative freedom, preventing hallucinations and unsafe instructions.

**Dynamic Fading**
The system's ability to automatically reduce prompting frequency as the child demonstrates mastery of a routine. Designed to build independence rather than dependency. The ultimate success metric is a child who no longer needs the device.

**ESP32**
A low-cost microcontroller board (~$3–4) used as a room scanner node. Powered by USB wall chargers, it connects to Wi-Fi, scans for the smartwatch's BLE advertisements, and reports RSSI values to the Cloud Backend.

**Hallucination (AI)**
When a language model generates plausible-sounding but factually incorrect, illogical, or inappropriate output. A critical risk in the HelperWatch context, mitigated by deterministic guardrails.

**Intercom Mode (Push-to-Talk)**
A caregiver mobile app feature that allows the parent to speak a command that the Cloud Backend integrates into the next verbal cue to the child, maintaining the familiar AI voice rather than jarring the child with a direct walkie-talkie transmission.

**Macro Routine Trigger**
A one-tap card in the caregiver mobile app that switches the AI's entire behavior profile — adjusting prompt frequency, biometric thresholds, and active scripts — to match a specific scenario (bedtime, leaving the house, cool-down).

**Meltdown Intercept**
The system's ability to detect early warning signs of emotional dysregulation (biometric spikes, pacing, motion patterns) and intervene with calming cues before a full meltdown manifests.

**Micro-Affirmation**
Brief, immediate positive reinforcement delivered by the AI after the child completes a small step. Designed to shift the background noise of the child's day from constant correction to consistent encouragement.

**Moonshine**
A lightweight, highly optimized speech-to-text (STT) engine that runs directly on the smartwatch to transcribe audio on-device. This ensures raw audio never leaves the wearable, protecting the family's privacy.

**Privacy Zone**
A caregiver-configured room or time window where audio capture is automatically paused on the watch, protecting bystander privacy during sensitive situations.

**RSSI (Received Signal Strength Indicator)**
A measurement of how strong a Bluetooth signal is. ESP32 room scanner nodes measure RSSI values from the smartwatch's advertisements and report them to the Cloud Backend to determine the child's room-level location.

**RSSI Fingerprinting**
A technique where the system learns the unique pattern of BLE signal strengths associated with each room during a calibration phase, and then matches incoming signals against those patterns to identify the current room.

**Room Scanner Node**
An active ESP32-based device placed in a room. It scans for the smartwatch's BLE advertisements, measures the RSSI, and reports it to the Cloud Backend over Wi-Fi to determine the child's room-level location.

**WebSocket**
A communication protocol that provides persistent, bidirectional communication between two endpoints over a single connection. Used for real-time data exchange (WSS) between the smartwatch, mobile app, and Cloud Backend.

---

## Related Documents

- [System Architecture](../design/System%20Architecture.md) — Technical overview using these terms
- [Indoor Positioning](../design/Indoor%20Positioning.md) — BLE and RSSI concepts in practice
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Privacy terms in context

