# Glossary

Key terms used throughout the HelperWatch documentation.

---

**BLE (Bluetooth Low Energy)**
A low-power wireless communication protocol. The smartwatch broadcasts BLE signals, and room scanner nodes scan for these signals to determine which room the child is in.

**Bridging Cue**
A verbal prompt that connects a current activity to an upcoming one during a transition, reducing the sense of loss from stopping a preferred activity. Example: *"Your blocks will be right here when you get back after dinner."* See: [Behavioral Use Cases — US-4](../design/Behavioral%20Use%20Cases.md).

**Bystander Privacy**
The concern that a wearable microphone captures audio from people other than the child — siblings, visitors, therapists — without their knowledge or consent. Mitigated through encrypted cloud transcription with immediate audio deletion, privacy zones, and quick-mute controls.

**Calm UI**
A user interface design philosophy (Weiser & Brown, 1997) that minimizes cognitive load by silently monitoring in the background and only demanding the user's attention when truly necessary. The caregiver mobile app follows this principle.

**Child Profile**
A structured, functional description of an individual child — how they communicate, what they can handle, what sets them off, and what helps them recover. The profile is not a medical record; it is an operating model that the response protocol engine reads to select appropriate responses. See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**Cloud Backend**
The central coordination engine of the HelperWatch system, hosted securely in the cloud. It manages user accounts, routine configurations, room location resolution, and AI orchestration.

**Cloud STT (Speech-to-Text)**
Speech-to-text transcription is performed on the Cloud Backend using a managed STT API (Whisper via Groq). The watch streams encrypted audio to the cloud, where it is transcribed in-memory and deleted immediately. No audio is ever stored at rest.

**Cognitive Prosthetic**
The conceptual framing of HelperWatch: a system that acts as an external extension of executive function for children who cannot independently manage sequencing, attention, and self-regulation.

**Cognitive Scaffolding**
Providing structured, step-by-step support to help a person complete tasks they cannot yet manage independently. HelperWatch provides scaffolding through real-time verbal cues, location-aware prompting, and micro-affirmations.

**Companion Mode**
A low-level ambient presence mode used primarily during bedtime. The system provides periodic soft check-ins, ambient sounds, or a breathing guide to give the child a sense of presence without requiring the caregiver to physically remain in the room. See: [Behavioral Use Cases — US-10](../design/Behavioral%20Use%20Cases.md).

**Context Qualifier**
A condition evaluated after a protocol trigger fires that narrows the response selection based on the child's current state, day quality score, recent events, active routine, time of day, or profile flags. The same trigger can produce different responses depending on context. See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**Data Sovereignty & Minimization**
The principle that the family's sensitive data is owned and controlled by them. In HelperWatch, this is achieved through encrypted audio transit with immediate deletion after transcription, encrypting all data in transit, and using a Cloud Backend that processes data transiently in memory without permanent storage.

**Day Quality Score**
A rolling aggregate maintained by the response protocol engine that answers: "What kind of day is this child having?" Positive events (routine completions, successful transitions) increase the score; negative events (meltdowns, conflicts, escalations) decrease it. Protocols use the score to adjust response selection — a child on a good day gets more latitude; a child on a bad day gets gentler handling. Resets daily. See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**Deterministic Guardrails**
Architectural constraints that prevent the AI from generating freeform, unpredictable language. The Cloud Backend acts as a router selecting from parent-approved scripts rather than having creative freedom, preventing hallucinations and unsafe instructions.

**Dynamic Fading**
The system's ability to automatically reduce prompting frequency as the child demonstrates mastery of a routine. Designed to build independence rather than dependency. The ultimate success metric is a child who no longer needs the device.

**Echolalia**
The repetition of words or phrases, either immediately after hearing them (immediate echolalia) or from memory (delayed echolalia / scripting). Common in the target population. Echolalia often serves communicative or self-regulatory functions and should not be treated as a failure state. See: [Behavioral Use Cases — US-1](../design/Behavioral%20Use%20Cases.md).

**Escalation Rules**
The part of a response protocol that defines what happens when the initial response does not produce the expected outcome. Escalation is graduated: wait → rephrase → simplify → shift strategy → alert caregiver → disengage. See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**ESP32**
A low-cost microcontroller board (~$3–4) used as a room scanner node. Powered by USB wall chargers, it connects to Wi-Fi, scans for the smartwatch's BLE advertisements, and reports RSSI values to the Cloud Backend.

**First-Step Anchoring**
A transition strategy where the system delivers only the first physical action required for a transition rather than the entire sequence. Example: *"Can you stand up?"* instead of *"Go to the kitchen, sit at the table, and eat dinner."* Reduces the cognitive load of transitions. See: [Behavioral Use Cases — US-4](../design/Behavioral%20Use%20Cases.md).

**Graduated Response**
A response strategy where the system's behavior changes across defined phases as a situation persists. Used in perseverative questioning (answer → redirect → acknowledge → boundary), echolalia (rephrase → simplify → reduce → alert), and bedtime (warm redirect → brief redirect → firm boundary). See: [Behavioral Use Cases](../design/Behavioral%20Use%20Cases.md).

**Hallucination (AI)**
When a language model generates plausible-sounding but factually incorrect, illogical, or inappropriate output. A critical risk in the HelperWatch context, mitigated by deterministic guardrails.

**Intercom Mode (Push-to-Talk)**
A caregiver mobile app feature that allows the parent to speak a command that the Cloud Backend integrates into the next verbal cue to the child, maintaining the familiar AI voice rather than jarring the child with a direct walkie-talkie transmission.

**Macro Routine Trigger**
A one-tap card in the caregiver mobile app that switches the AI's entire behavior profile — adjusting prompt frequency, biometric thresholds, and active scripts — to match a specific scenario (bedtime, leaving the house, cool-down).

**Meltdown Intercept**
The system's ability to detect early warning signs of emotional dysregulation (biometric spikes, pacing, motion patterns) and intervene with calming cues before a full meltdown manifests. The full meltdown arc includes build-up detection, peak protocol (prompt suppression), and recovery re-engagement. See: [Behavioral Use Cases — US-7](../design/Behavioral%20Use%20Cases.md).

**Micro-Affirmation**
Brief, immediate positive reinforcement delivered by the AI after the child completes a small step. Designed to shift the background noise of the child's day from constant correction to consistent encouragement.

**Perseveration**
The persistent repetition of a behavior, question, or topic beyond what is contextually appropriate. In the HelperWatch context, perseverative questioning (asking the same question repeatedly) is a common behavioral pattern addressed with a graduated response strategy. See: [Behavioral Use Cases — US-2](../design/Behavioral%20Use%20Cases.md).

**Privacy Zone**
A caregiver-configured room or time window where audio capture is automatically paused on the watch, protecting bystander privacy during sensitive situations.

**Proactive Engagement**
System-initiated interaction designed to fill attention gaps before the child escalates. Unlike reactive prompting (responding to what the child says or does), proactive engagement reaches out first: *"What are you building?"* Configurable via the attention check-in interval in the child profile. See: [Behavioral Use Cases — US-3](../design/Behavioral%20Use%20Cases.md).

**Protocol Priority**
A fixed hierarchy (9 levels) that determines which response protocol takes precedence when multiple protocols trigger simultaneously. Safety and meltdown peak are highest priority; companion/ambient mode is lowest. Lower-priority protocols are suspended, not cancelled, and resume when the higher-priority protocol exits. See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**Recent Event Buffer**
A short-term memory maintained by the response protocol engine, tracking significant events (meltdowns, conflicts, successful routines, caregiver interjections) with time decay. Events in the buffer influence protocol selection within their decay window — e.g., a meltdown 20 minutes ago causes the system to reduce prompt intensity even if current biometrics are stable. See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**Response Protocol**
A structured decision unit in the response protocol engine. Every protocol has five parts: trigger conditions (what activates it), context qualifiers (what narrows the response), response actions (what the system does), escalation rules (what happens if it doesn't work), and exit conditions (when it deactivates). See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**Room Scanner Node**
An active ESP32-based device placed in a room. It scans for the smartwatch's BLE advertisements, measures the RSSI, and reports it to the Cloud Backend over Wi-Fi to determine the child's room-level location.

**RSSI (Received Signal Strength Indicator)**
A measurement of how strong a Bluetooth signal is. ESP32 room scanner nodes measure RSSI values from the smartwatch's advertisements and report them to the Cloud Backend to determine the child's room-level location.

**RSSI Fingerprinting**
A technique where the system learns the unique pattern of BLE signal strengths associated with each room during a calibration phase, and then matches incoming signals against those patterns to identify the current room.

**Shutdown (Sensory)**
A protective neurological response to sensory overload in which the child withdraws, goes non-verbal, becomes rigid or unresponsive. Not a meltdown — shutdown is the nervous system reducing output to reduce input. The system's response is to suppress all prompts and reduce its own sensory footprint. See: [Behavioral Use Cases — US-8](../design/Behavioral%20Use%20Cases.md).

**Template Profile**
A pre-built child profile that provides reasonable default values for a common presentation (e.g., "non-verbal with severe executive dysfunction," "verbal, impulsive, attention-seeking"). Templates are starting points — caregivers customize them for their specific child. See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**Trigger Condition**
The event or signal that activates a response protocol. Triggers can be transcript-based, biometric-based, location-based, time-based, behavioral composites, or system-initiated. Triggers can be combined with AND/OR logic. See: [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md).

**WebSocket**
A communication protocol that provides persistent, bidirectional communication between two endpoints over a single connection. Used for real-time data exchange (WSS) between the smartwatch, mobile app, and Cloud Backend.

---

## Related Documents

- [System Architecture](../design/System%20Architecture.md) — Technical overview using these terms
- [Indoor Positioning](../design/Indoor%20Positioning.md) — BLE and RSSI concepts in practice
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Privacy terms in context
- [Behavioral Use Cases](../design/Behavioral%20Use%20Cases.md) — Behavioral scenarios referenced by many terms
- [Adaptive Response Architecture](../design/Adaptive%20Response%20Architecture.md) — Profile and protocol concepts defined in detail
