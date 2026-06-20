# Cloud Backend

## Overview

The Cloud Backend is the central coordination engine of the HelperWatch system, deployed in a secure cloud environment. By running in the cloud, the system avoids local hardware dependencies, installation complexity, and local network configuration challenges. 

The backend receives location reports from ESP32 room scanner nodes, processes biometric and transcribed text data from the smartwatch, coordinates routine transitions, runs the AI classifier/router, and provides real-time state synchronization to the caregiver mobile app.

```
┌────────────────────────────────────────────────────────┐
│                    Cloud Backend                       │
│                                                        │
│  ┌───────────────────────┐    ┌─────────────────────┐  │
│  │   Telemetry & State   │    │  AI Orchestration   │  │
│  │   Endpoints (WSS)     │◄──►│  (LLM Classifier)   │  │
│  └───────────────────────┘    └─────────────────────┘  │
│              ▲                           ▲             │
│              │                           │             │
│              ▼                           ▼             │
│  ┌───────────────────────┐    ┌─────────────────────┐  │
│  │  Routine Engine &     │    │  Encrypted User DB  │  │
│  │  Fading Logic         │    │  (Routines, Logs)   │  │
│  └───────────────────────┘    └─────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

## Core Functions

### Telemetry Reception and State Management
The backend maintains persistent, secure WebSocket (WSS) connections with all active devices registered under a caregiver's account:
- **ESP32 Scanner Nodes:** Receive real-time RSSI readings from each room.
- **Smartwatch:** Receive transcribed speech text (from on-watch STT), heart rate readings, and accelerometer data.
- **Caregiver Mobile App:** Sync state updates and receive real-time alerts.

### AI Orchestration (LLM Classifier-Router)
The backend coordinates LLM inference via a managed cloud API (e.g., Groq, OpenAI, or a dedicated private cloud instance). Because the backend hardware is controlled, inference latency is kept in the sub-second range.

The LLM is strictly used as a **classifier and router**, not a creative writer:
1. It receives a structured payload containing:
   - The child's speech transcript (transcribed on-watch).
   - The child's current room (computed from ESP32 RSSI values).
   - Biometric state (heart rate, motion classification).
   - The active routine and current step.
   - The caregiver-defined script set.
2. The LLM selects the most appropriate response from the caregiver's pre-written script or fits parameters into highly constrained templates.
3. This deterministic routing prevents hallucinations or inappropriate instructions while remaining highly responsive.

### Routine Engine and Dynamic Fading
- Storing caregiver-configured schedules and step-by-step routine checklists.
- **Dynamic Fading:** The engine logs task completion rates and automatically decreases prompt frequency as the child shows mastery, prompting only when delays or distress are detected.

### Real-Time Alerts
- Monitors heart rate spikes and pacing patterns (accelerometer).
- Sends instant push notifications (via Apple APNs / Google FCM) to the caregiver mobile app during potential meltdowns or if the smartwatch loses connection.

---

## Transient Data and Privacy-by-Design

Shifting to the cloud does not mean abandoning privacy. The Cloud Backend is built on a **zero-persistence, transient-only pipeline** for sensitive data:

1. **On-Watch Transcription:** The smartwatch performs speech-to-text locally. Only text transcripts, never raw audio, are sent to the Cloud Backend.
2. **Transient In-Memory Processing:** Incoming transcripts and active biometrics are held in-memory for the duration of the LLM prompting loop and are discarded immediately afterward.
3. **Encrypted Storage:** User accounts, routine configurations, and historical trends (if caregiver-enabled) are stored in a database encrypted at rest.
4. **Caregiver Log Control:** Caregivers can disable historical trend logging entirely. When disabled, no text transcripts, location logs, or biometric trends are stored.
5. **On-Demand Purging:** Caregivers can delete their account and all associated cloud data instantly via the mobile app.

---

## API Specifications

### 1. ESP32 RSSI Telemetry (HTTPS POST or WSS)
Sent by each ESP32 node to report signal strength from the watch.
```json
{
  "node_id": "esp32_kitchen_01",
  "device_mac": "XX:XX:XX:XX:XX:XX",
  "rssi": -65,
  "timestamp": 1782384910
}
```

### 2. Smartwatch Telemetry (WSS)
Sent by the smartwatch to report biometrics, accelerometer states, and on-watch transcriptions.
```json
{
  "device_id": "watch_leo_01",
  "transcript": "i am brushing my teeth",
  "heart_rate": 82,
  "motion": "still",
  "timestamp": 1782384912
}
```

### 3. Caregiver Intercom Injection (HTTPS POST)
Sent when the parent uses Push-to-Talk to inject a command into the next cue.
```json
{
  "account_id": "user_parent_abc",
  "injected_text": "Tell Leo it's almost time for dinner and to put his blocks away."
}
```

---

## Related Documents

- [System Architecture](System%20Architecture.md) — How the cloud backend connects to other nodes
- [Indoor Positioning](Indoor%20Positioning.md) — Room fingerprinting math and ESP32 scanner setup
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Cloud privacy pledge and data lifecycle
