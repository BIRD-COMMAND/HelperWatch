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
│  │  Fading Logic         │    │  (PostgreSQL)       │  │
│  └───────────────────────┘    └─────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

## Technology Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| **Runtime** | Node.js (TypeScript) | Shares the TypeScript language with the React Native mobile app, enabling shared type definitions and API contracts. Native WebSocket support. |
| **HTTP/WS Framework** | Fastify + `ws` (or Hono) | High-performance HTTP and native WebSocket handling. Avoids the overhead of Express. |
| **Database** | PostgreSQL (via Supabase or managed cloud Postgres) | Mature, encrypted-at-rest by default on all major providers. Strong JSON support for routine configs. |
| **Hosting** | Container-based platform (Fly.io, Railway, or AWS ECS/Fargate) | Persistent WebSocket connections require a stateful, always-on server. Pure serverless (Lambda, Cloud Functions) is not viable due to cold starts and connection limits. |
| **Auth** | Firebase Auth or Supabase Auth (caregiver accounts) + JWT (device tokens) | Caregiver accounts use a managed auth provider. Devices (watch, ESP32) authenticate with per-device JWT tokens issued during provisioning. |
| **LLM API** | Groq (primary), OpenAI (fallback) | Groq provides sub-second inference for classifier/router tasks at low cost. OpenAI serves as a fallback if Groq is unavailable. |
| **Push Notifications** | Firebase Cloud Messaging (FCM) / Apple APNs | Industry-standard, free push notification delivery. |

## Core Functions

### Telemetry Reception and State Management
The backend maintains persistent, secure WebSocket (WSS) connections with all active devices registered under a caregiver's account:
- **ESP32 Scanner Nodes:** Receive real-time RSSI readings from each room.
- **Smartwatch:** Receive encrypted audio streams for transcription, plus heart rate readings and accelerometer data.
- **Caregiver Mobile App:** Sync state updates and receive real-time alerts.

### AI Orchestration (LLM Classifier-Router)
The backend coordinates LLM inference via a managed cloud API (Groq as primary, OpenAI as fallback). Because the backend hardware is controlled, inference latency is kept in the sub-second range.

The LLM is strictly used as a **classifier and router**, not a creative writer:
1. It receives a structured payload containing:
   - The child's speech transcript (transcribed by the Cloud Backend's STT service).
   - The child's current room (computed from ESP32 RSSI values).
   - Biometric state (heart rate, motion classification).
   - The active routine and current step.
   - The caregiver-defined script set.
2. The LLM selects the most appropriate response from the caregiver's pre-written script or fits parameters into highly constrained templates.
3. This deterministic routing prevents hallucinations or inappropriate instructions while remaining highly responsive.

### Routine Engine, Prompt Hierarchy, and Reinforcement
- Storing caregiver-configured schedules, step-by-step routine checklists, and per-routine settings (prompt hierarchy direction, reinforcement schedule).
- **Prompt Hierarchy Management:** Each routine is configured with a prompting direction — least-to-most (escalate intrusiveness only if the child does not respond) or most-to-least (start with full support, fade over days). The engine moves through prompts in the configured direction and tracks prompt dependency signals. See: [Adaptive Response Architecture — Prompt Hierarchy Model](Adaptive%20Response%20Architecture.md).
- **Reinforcement Schedule Selection:** Per-routine reinforcement schedules (continuous, variable ratio, chain-end, or minimal) control how frequently micro-affirmations are delivered. The engine varies affirmation language from a library and suggests fading reinforcement density as the child demonstrates mastery. See: [Adaptive Response Architecture — Reinforcement Schedule Model](Adaptive%20Response%20Architecture.md).
- **Dynamic Fading:** The engine logs task completion rates and automatically decreases prompt frequency as the child shows mastery, prompting only when delays or distress are detected.
- **Sub-Domain-Aware Strategies:** The response protocol engine reads the child's executive function sub-domain profile (working memory, cognitive flexibility, inhibitory control) to select scaffolding strategies matched to the child's specific bottlenecks. See: [Adaptive Response Architecture — Executive Function Profile](Adaptive%20Response%20Architecture.md).

### Real-Time Alerts
- Monitors heart rate spikes and pacing patterns (accelerometer).
- Sends instant push notifications (via FCM / APNs) to the caregiver mobile app during potential meltdowns or if the smartwatch loses connection.

---

## Authentication and Device Identity

### Caregiver Accounts
Caregivers register via the mobile app using a managed auth provider (Firebase Auth or Supabase Auth). This handles email/password login, session management, and token refresh.

### Device Authentication
Devices authenticate with the Cloud Backend using **per-device JWT tokens**:

- **ESP32 Scanner Nodes:** During the WebUSB/WebSerial flashing step, a unique API key is burned into the firmware alongside the Wi-Fi credentials. The ESP32 presents this key on each connection, and the backend validates it against the caregiver's account.
- **Smartwatch:** During the 6-digit pairing flow, the backend issues a device-specific JWT to the watch. The watch stores this token and uses it for all subsequent WSS connections.
- **Mobile App:** Uses the caregiver's session token from the managed auth provider.

---

## ESP32 OTA Firmware Updates

ESP32 scanner nodes support over-the-air (OTA) firmware updates:

1. On boot and on a configurable schedule (default: daily), each ESP32 node sends an HTTPS request to the Cloud Backend's `/firmware/check` endpoint with its current firmware version.
2. If a newer version is available, the backend responds with a signed firmware URL.
3. The ESP32 downloads the firmware binary over HTTPS and writes it to its secondary OTA partition.
4. On successful verification, the ESP32 reboots into the new firmware.

This allows firmware bugs and feature updates to be deployed without physical access to the nodes.

---

## Transient Data and Privacy-by-Design

Shifting to the cloud does not mean abandoning privacy. The Cloud Backend is built on a **zero-persistence, transient-only pipeline** for sensitive data:

1. **Cloud Transcription:** The smartwatch streams encrypted audio to the Cloud Backend over WSS. The Cloud Backend transcribes the audio using a managed STT API (Whisper via Groq). Raw audio is processed in-memory and deleted immediately after transcription — never written to persistent storage.
2. **Transient In-Memory Processing:** Incoming text transcripts and biometric data are held in-memory for the duration of the LLM prompting loop and are discarded immediately afterward.
3. **Encrypted Storage:** User accounts, routine configurations, and historical trends (if caregiver-enabled) are stored in PostgreSQL, encrypted at rest.
4. **Caregiver Log Control:** Caregivers can disable historical trend logging entirely. When disabled, no text transcripts, location logs, or biometric trends are stored.
5. **On-Demand Purging:** Caregivers can delete their account and all associated cloud data instantly via the mobile app.

---

## Cloud Cost Estimate (Per-Family)

| Component | Estimated Monthly Cost |
|-----------|----------------------|
| Cloud hosting (small container, always-on for WSS) | $5–7 |
| Managed PostgreSQL (small instance) | $0–5 (free tier on Supabase/Neon) |
| LLM API tokens (Groq, classifier-only usage) | $1–3 |
| Push notifications (FCM/APNs) | Free |
| **Total** | **$6–15** |

These estimates assume a single-family deployment. Multi-tenant hosting would reduce per-family costs.

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

### 4. ESP32 Firmware Check (HTTPS GET)
Sent by each ESP32 node on boot and on a daily schedule.
```
GET /firmware/check?version=1.0.2&node_id=esp32_kitchen_01
```
Response (if update available):
```json
{
  "update_available": true,
  "version": "1.0.3",
  "firmware_url": "https://cdn.helperwatch.io/firmware/1.0.3.bin",
  "checksum": "sha256:abc123..."
}
```

---

## Related Documents

- [System Architecture](System%20Architecture.md) — How the cloud backend connects to other nodes
- [Indoor Positioning](Indoor%20Positioning.md) — Room fingerprinting math and ESP32 scanner setup
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Cloud privacy pledge and data lifecycle
- [Behavioral Use Cases](Behavioral%20Use%20Cases.md) — Behavioral scenarios informing AI orchestration and routine engine design
- [Adaptive Response Architecture](Adaptive%20Response%20Architecture.md) — Child profiles, protocol engine, and context-aware decision logic
