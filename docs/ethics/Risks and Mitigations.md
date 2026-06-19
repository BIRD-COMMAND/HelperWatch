# Risks and Mitigations

This document catalogs every identified risk associated with the HelperWatch system, along with its mitigation strategy and current status.

## Risk Classification

Each risk is classified by mitigation status:

- **Mitigatable** — Fully addressed through system architecture and design.
- **Partially mitigatable** — Reduced through design but requiring caregiver awareness and responsible use.
- **Design-philosophy-only** — Cannot be eliminated through code; addressed through education, documentation, and intended-use framing.

---

## Risk Register

### 1. Data Sovereignty Failure

**Classification:** Mitigatable

**The risk:** If raw data (room tracking, emotional states, audio recordings of a vulnerable minor in their own home) reaches a public cloud or a leaked database, it is a catastrophic violation of the child's dignity and safety. Attackers could listen into the home. Corporations could profile a disabled child's behavioral vulnerabilities.

**Mitigation:**
- **On-watch speech-to-text.** The smartwatch transcribes all voice audio locally. Raw audio never leaves the smartwatch, avoiding the risk of cloud audio interception or leaks.
- **Transient processing.** The Cloud Backend processes text transcripts in memory and discards them immediately after cue classification.
- **Strict encryption.** All telemetry data (biometrics, transcripts) is encrypted in transit using secure WebSockets (WSS/TLS). Database entries are encrypted at rest.
- **Caregiver log controls.** Caregivers can turn logging off entirely, preventing the creation of any persistent behavioral logs in the cloud.
- **Open-source auditability.** Parents, security advocates, and clinical partners can inspect the source code to verify these safeguards.

See: [Privacy and Data Sovereignty](Privacy%20and%20Data%20Sovereignty.md)

---

### 2. Over-Reliance and Cognitive Atrophy

**Classification:** Partially mitigatable

**The risk:** If a machine manages every micro-second of a child's day, the child may lose the ability to develop intrinsic coping mechanisms. They could become completely dependent on the "voice in their ear" to function at all.

**Mitigation:**
- **Dynamic fading architecture.** The system tracks task success over time and automatically reduces prompting frequency as the child demonstrates mastery. It pulls back, tests autonomy, and re-engages only when failure states or extreme delays are detected.
- **Configurable independence targets.** Caregivers can set fading aggressiveness and define what "mastery" looks like for each routine.

**Why partially mitigatable:** The fading algorithm can reduce prompts, but the caregiver must also be willing to accept the discomfort of watching their child struggle without AI assistance during the fading phase. This requires education and support, not just code.

---

### 3. AI Hallucination / Confidently Wrong Instructions

**Classification:** Mitigatable

**The risk:** Large language models can generate plausible-sounding but factually incorrect, illogical, or inappropriate statements. If the AI misinterprets a chaotic audio stream (e.g., confusing playful noise with a physical fight) and issues a confusing or contradictory verbal cue to a child who is highly literal or easily overwhelmed, it could actively cause a meltdown.

**Mitigation:**
- **Deterministic guardrails.** The AI does not have freeform creative freedom. It acts as a router that selects from a strict, parent-approved script of pre-written commands, or generates output through highly constrained, predictable syntax templates.
- **No novel safety-critical instructions.** The AI never tells the child to do something the parent has not explicitly pre-approved.

See: [Cloud Backend — Deterministic Guardrails](../design/Cloud%20Backend.md)

---

### 4. Bystander Privacy Violation

**Classification:** Partially mitigatable

**The risk:** The watch microphone captures everything in its physical radius. Neighbors visiting, siblings discussing personal matters, telehealth sessions — all are recorded, transcribed, and parsed by a machine without the bystander's knowledge or consent.

**Mitigation:**
- **On-watch transcription and immediate deletion.** Raw audio is transcribed on-device and instantly destroyed. No audio recordings are ever stored or sent to the cloud.
- **Privacy zones.** Caregivers can designate rooms or time windows where audio capture is paused.
- **Quick-mute gesture.** Tapping the watch face pauses audio processing for a configurable duration.
- **Caregiver education.** Documentation clearly instructs caregivers to inform visitors that audio monitoring is active.

**Why partially mitigatable:** The system can delete audio instantly and offer mute controls, but it cannot prevent the initial capture of bystander audio during active monitoring. Responsibility for informing visitors falls on the caregiver.

---

### 5. Stigma and Social Impact

**Classification:** Partially mitigatable

**The risk:** As children grow, their self-awareness and awareness of others matures. A bulky device or a watch that talks aloud in front of peers, siblings, or visitors can trigger feelings of shame, embarrassment, or social isolation (PMC, 2024).

**Mitigation:**
- **Earbud-first default.** The system defaults to quiet, low-profile haptic buzzes or private audio through wireless earbuds, keeping the cognitive assistance invisible to the outside world.
- **Discreet hardware.** The system targets commodity smartwatches that look like any other watch a child might wear — not a medical device.
- **Configurable output modes.** Caregivers can choose speaker, earbud, or haptic-only modes based on context.

**Why partially mitigatable:** The system can be made as discreet as possible, but it cannot eliminate the child's own internal awareness that they are wearing an assistive device that their peers are not.

---

### 6. Loss of Purely Human Intuition

**Classification:** Design-philosophy-only

**The risk:** If an AI becomes the primary interpreter of a child's emotional state, caregivers might begin to rely more on dashboard alerts than their own parental intuition. They might miss subtle, unquantifiable shifts in their child's eyes or body language because "the app says everything is normal."

**Mitigation:**
- **Framing as supplementary.** All documentation, onboarding, and marketing explicitly position HelperWatch as a tool to catch the gaps — not a replacement for human presence.
- **No "all clear" language.** The app never displays a message like "Your child is fine." It reports observable data (room, activity, heart rate) and leaves interpretation to the parent.
- **Encouraging direct engagement.** The system can be designed to periodically prompt the caregiver: "It's been a while since you checked in with your child directly."

**Why design-philosophy-only:** This risk is inherent to any assistive technology that reduces the burden on a caregiver. It cannot be coded away. It can only be addressed through honest communication about the system's limitations.

---

### 7. Internet and Cloud Dependency (System Reliability)

**Classification:** Partially mitigatable

**The risk:** Since the system relies on a Cloud Backend, a temporary internet outage or cloud service interruption would break the end-to-end prompting loop. If this happens during a critical transition or meltdown build-up, the child could be left without cues, and the caregiver might not receive real-time alerts.

**Mitigation:**
- **Smartwatch Offline Fallback:** The smartwatch app caches the active routine's script set. If the watch loses Wi-Fi or internet connection, it switches to a local offline mode where it plays basic timer-based prompts and issues haptic warnings directly.
- **Connection Loss Alerts:** The caregiver mobile app receives an immediate notification if the smartwatch or cloud backend detects a connectivity loss.

---

### 8. Node Deployment Complexity and Flashing

**Classification:** Mitigatable

**The risk:** Caregivers need to flash ESP32 boards and deploy them around the house, which can be an intimidating technical barrier. Incorrect placement or incorrect Wi-Fi configuration would lead to poor room tracking accuracy or silent failure to report RSSI.

**Mitigation:**
- **Web Flashing Tool:** We provide a simplified web-based flashing tool (using WebUSB/WebSerial) so caregivers can flash ESP32s from a browser in one click, enter Wi-Fi details, and register them to their account.
- **Out-of-the-Box Node Packs:** The project will provide options to purchase pre-flashed, pre-packaged scanner node kits.
- **Signal Diagnostics:** The caregiver mobile app features a dedicated room signal calibration and diagnostic screen that visualizes ESP32 connectivity and signal strength at a glance.

---

## References

- Common Sense Media. (2026, February). *Study warns AI toys pose 'unacceptable risks' to young children.* CapRadio.
- Evaluating Users' Experiences of a Child Multimodal Wearable Device: Mixed Methods Approach. (2024). *PubMed Central (PMC).* National Institutes of Health.

## Related Documents

- [Safety and Ethics Overview](Safety%20and%20Ethics%20Overview.md) — Ethical framework and hard lines
- [Privacy and Data Sovereignty](Privacy%20and%20Data%20Sovereignty.md) — Data handling details
- [Mission and Values](../project/Mission%20and%20Values.md) — Why these guardrails exist

