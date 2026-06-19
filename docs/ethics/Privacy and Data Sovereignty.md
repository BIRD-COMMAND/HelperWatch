# Privacy and Data Sovereignty

## Core Principle

Privacy is not a feature — it is a hard-coded architectural requirement. HelperWatch is designed so that all sensitive data (audio recordings and biometric readings) is processed and stored locally on the family's own hardware. No raw data leaves the home network by default.

## Data Types and Handling

### Audio Data

Audio is the most sensitive data the system handles. It captures not only the child's voice but also the voices of siblings, parents, visitors, and any ambient sound in the home.

**Lifecycle:**

1. The watch microphone captures audio and compresses it locally.
2. Compressed audio is transmitted over the home Wi-Fi network to the local server hub.
3. The local server transcribes the audio to text using an embedded speech-to-text model (Whisper.cpp or Moonshine).
4. **Raw audio is deleted immediately after transcription.** No audio files are retained on the server or the watch.
5. The resulting text transcript is used for AI processing and then stored in local logs (if logging is enabled by the caregiver).

**Hard rules:**
- Raw audio is never written to persistent storage on any device.
- Raw audio is never transmitted outside the local network.
- Transcription happens entirely on the local machine.

### Biometric Data

Heart rate and accelerometer data are sampled by the watch and transmitted to the local server.

**Lifecycle:**

1. The watch samples biometric data at a configurable interval.
2. Data is transmitted to the local server over the home Wi-Fi network.
3. The server uses the data for real-time analysis (meltdown intercept, activity classification).
4. Historical biometric data is stored in local logs for trend reporting.

**Hard rules:**
- Biometric data is never transmitted outside the local network.
- Trend data is stored locally and accessible only through the caregiver mobile app.

### Location Data

Room-level positioning is determined by BLE beacon signal strength. The system knows which room the child is in, not their precise coordinates.

**Hard rules:**
- Location data is processed and stored locally.
- No location data is transmitted externally.

### Text Transcripts and AI Logs

Transcripts and AI interaction logs are the least sensitive data type but still contain personal information about the child's behavior and routines.

**Hard rules:**
- Stored locally on the server machine only.
- Accessible to the caregiver through the mobile app (over local network or encrypted tunnel).
- Never transmitted externally unless the caregiver explicitly exports data for clinical use.

## Bystander Privacy

A microphone worn on a child's wrist captures everything in its physical radius. This includes the voices and conversations of people who have not consented to being recorded.

### Mitigations

- **Immediate audio deletion.** Raw audio is discarded the moment transcription is complete. No audio archive exists to be breached or reviewed.
- **Privacy zones.** Caregivers can define rooms or times where audio capture is paused entirely.
- **Quick-mute.** A physical gesture on the watch (e.g., double-tap the face) pauses audio processing for a configurable duration (e.g., 30 minutes).
- **Earbud-first default.** The system defaults to private audio output through earbuds, reducing the chance that bystanders become aware of and uncomfortable with the monitoring.

### Caregiver Responsibility

The public-facing documentation must clearly communicate that:

- The watch microphone is always active when monitoring is on.
- Visitors should be informed that audio monitoring is in use.
- Privacy zones and quick-mute should be used during sensitive conversations, therapy sessions, or visits.

## Cloud API Fallback

For families whose hardware cannot run local AI models, the system offers a **Private Cloud API** toggle.

When enabled:

- Only text transcripts (not raw audio) are sent to the cloud API.
- Transcripts are anonymized and stripped of identifying metadata before transmission.
- Data is encrypted end-to-end.
- The caregiver explicitly opts in, understanding the trade-off.
- No data is stored on the cloud provider's servers beyond the API request lifecycle (subject to the provider's data retention policy, which should be documented in the setup guide).

This is a transparent, informed choice — not a default behavior.

## Data Export and Deletion

- Caregivers can export anonymized trend reports (room transitions, task completion rates, biometric patterns) for sharing with clinicians or therapists.
- Caregivers can delete all local data at any time through the server hub interface.
- Uninstalling the server hub application removes all stored data.

<!-- TODO: Specific data retention policies, automated purging schedules, and GDPR/COPPA compliance considerations will be documented as the system matures. -->

## Related Documents

- [Safety and Ethics Overview](Safety%20and%20Ethics%20Overview.md) — Ethical framework
- [Risks and Mitigations](Risks%20and%20Mitigations.md) — Risk register including data sovereignty failure
- [Local Server Hub](../design/Local%20Server%20Hub.md) — Where data processing happens
- [FAQ](../guides/FAQ.md) — Common privacy questions
