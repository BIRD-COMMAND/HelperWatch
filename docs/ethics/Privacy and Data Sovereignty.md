# Privacy and Data Sovereignty

## Core Principle

Privacy is not a feature — it is a hard-coded architectural requirement. HelperWatch is designed so that all sensitive data is handled with a strict **privacy-by-design** cloud posture:

- **Encrypted audio transit:** The watch streams audio encrypted (TLS) to the Cloud Backend for transcription. Audio is processed transiently in-memory and deleted immediately after transcription. No audio is ever stored at rest.
- **Transient processing:** Text transcripts, biometrics, and signal reports are held transiently in-memory on the Cloud Backend during the AI prompting loop and deleted immediately after.
- **Encrypted storage:** Account data and routine logs (if enabled) are encrypted at rest.
- **Caregiver sovereignty:** The caregiver has absolute control to disable logging, export records, or delete all cloud records instantly.

---

## Data Types and Handling

### Audio Data

Audio is the most sensitive data the system handles. It captures not only the child's voice but also the voices of siblings, parents, visitors, and any ambient sound in the home.

**Lifecycle:**

1. The watch microphone captures audio and streams it encrypted (TLS) to the Cloud Backend over a persistent WSS connection.
2. The Cloud Backend transcribes the audio using a managed STT API (Whisper via Groq).
3. **Raw audio is processed in-memory and deleted immediately after transcription.** Audio is never written to persistent storage on any server.
4. The Cloud Backend processes the resulting text transcript transiently in-memory through the LLM classifier.
5. The transcript is immediately deleted from memory after cue selection, unless the caregiver has enabled historical logging for trend reporting.

**Hard rules:**
- Raw audio is never written to persistent storage on any device or server.
- Raw audio is never retained after transcription is complete.
- Raw audio is encrypted in transit using TLS.
- No audio recordings are ever stored at rest.

### Biometric Data

Heart rate and accelerometer data are sampled by the watch and transmitted to the Cloud Backend.

**Lifecycle:**

1. The watch samples biometric data at a configurable interval.
2. Data is encrypted and transmitted to the Cloud Backend over secure WebSocket (WSS).
3. The backend uses the data for real-time analysis (meltdown intercept, activity classification).
4. Biometric data is deleted immediately from memory after processing, unless historical logging is enabled by the caregiver.

**Hard rules:**
- All biometric transmissions are encrypted using TLS/HTTPS.
- Biometric records are stored in the database only if explicitly enabled by the caregiver.

### Location Data

Room-level positioning is determined by ESP32 room scanner nodes measuring the signal strength of the watch's BLE advertisements.

**Lifecycle:**

1. ESP32 room scanner nodes detect the watch's BLE broadcast, measure RSSI, and transmit these reports to the Cloud Backend over Wi-Fi.
2. The Cloud Backend compares the reports to resolve room-level location.
3. Raw RSSI reports are discarded immediately. Resolved location state is deleted after use unless trend logging is enabled.

**Hard rules:**
- Raw signal reports are never stored.
- Location history is encrypted at rest if trend logging is active.

### Text Transcripts and AI Logs

Transcripts and AI interaction logs are the least sensitive data type but still contain personal information about the child's behavior and routines.

**Hard rules:**
- Stored securely in an encrypted cloud database.
- Accessible only to the caregiver through the mobile app with valid credentials.
- Caregivers can disable transcript logging entirely in settings, preventing any text archives from being kept.

---

## Bystander Privacy

A microphone worn on a child's wrist captures everything in its physical radius. This includes the voices and conversations of people who have not consented to being recorded.

### Mitigations

- **Immediate audio deletion.** Audio streamed to the Cloud Backend is transcribed in memory and deleted immediately. No audio recordings are ever stored at rest, eliminating the risk of audio archives being breached or reviewed.
- **Privacy zones.** Caregivers can define rooms or times where audio capture and transcription are paused entirely on the watch.
- **Quick-mute.** A physical gesture on the watch (e.g., double-tap the face) pauses audio processing for a configurable duration (e.g., 30 minutes).
- **Earbud-first default.** The system defaults to private audio output through earbuds, reducing the chance that bystanders become aware of and uncomfortable with the monitoring.

### Caregiver Responsibility

The public-facing documentation must clearly communicate that:
- The watch microphone is active when routine monitoring is running.
- Visitors should be informed that audio monitoring is in use.
- Privacy zones and quick-mute should be used during sensitive conversations, therapy sessions, or visits.

---

## Data Export and Deletion

- Caregivers can export anonymized trend reports (room transitions, task completion rates, biometric patterns) for sharing with clinicians or therapists.
- Caregivers can delete all cloud data and logs at any time through the caregiver mobile app interface.
- Deleting the caregiver account instantly and permanently purges all user data, routines, and logs from the cloud database.

---

## Related Documents

- [Safety and Ethics Overview](Safety%20and%20Ethics%20Overview.md) — Ethical framework
- [Risks and Mitigations](Risks%20and%20Mitigations.md) — Risk register including data sovereignty mitigations
- [Cloud Backend](../design/Cloud%20Backend.md) — Where data processing happens
- [FAQ](../guides/FAQ.md) — Common privacy questions
