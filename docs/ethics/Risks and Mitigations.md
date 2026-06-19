# Risks and Mitigations

This document catalogs every identified risk associated with the HelperWatch system, along with its mitigation strategy and current status.

## Risk Classification

Each risk is classified by mitigation status:

- **Mitigatable** — Fully addressed through system architecture and design.
- **Partially mitigatable** — Reduced through design but requiring caregiver awareness and responsible use.
- **Design-philosophy-only** — Cannot be eliminated through code; addressed through education, documentation, and intended-use framing.

## Risk Register

### 1. Data Sovereignty Failure

**Classification:** Mitigatable

**The risk:** If raw data (room tracking, emotional states, audio recordings of a vulnerable minor in their own home) reaches a commercial cloud or a leaked database, it is a catastrophic violation of the child's dignity and safety. Attackers could listen into the home. Corporations could profile a disabled child's behavioral vulnerabilities.

**Mitigation:**

- **Total local sovereignty.** The system processes all audio and biometric data on the family's local hardware. No raw data leaves the home network by default.
- **Local AI models.** Speech-to-text (Whisper.cpp/Moonshine) and language models (Ollama) run entirely on the home machine.
- **Immediate audio deletion.** Raw audio is purged from memory the instant transcription is complete. No audio archive exists to breach.
- **Cloud API is opt-in only.** If a family uses the cloud fallback, data is anonymized and stripped of metadata before transmission. The caregiver makes this choice transparently.
- **Open-source auditability.** Anyone can verify these claims by reading the code.

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

- **Deterministic guardrails.** The AI does not have free-reign creative freedom. It acts as a router that selects from a strict, parent-approved script of pre-written commands, or generates output through highly constrained, predictable syntax templates.
- **No novel safety-critical instructions.** The AI never tells the child to do something the parent has not explicitly pre-approved.

See: [Local Server Hub — Deterministic Guardrails](../design/Local%20Server%20Hub.md)

---

### 4. Bystander Privacy Violation

**Classification:** Partially mitigatable

**The risk:** The watch microphone captures everything in its physical radius. Neighbors visiting, siblings discussing personal matters, telehealth sessions — all are recorded, transcribed, and parsed by a machine without the bystander's knowledge or consent.

**Mitigation:**

- **Immediate audio deletion.** Raw audio is destroyed the instant transcription finishes. No recordings persist.
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
- **Encouraging direct engagement.** The system can be designed to periodically prompt the caregiver: "It's been a while since you checked in with [child] directly."

**Why design-philosophy-only:** This risk is inherent to any assistive technology that reduces the burden on a caregiver. It cannot be coded away. It can only be addressed through honest communication about the system's limitations.

## References

- Common Sense Media. (2026, February). *Study warns AI toys pose 'unacceptable risks' to young children.* CapRadio.
- Evaluating Users' Experiences of a Child Multimodal Wearable Device: Mixed Methods Approach. (2024). *PubMed Central (PMC).* National Institutes of Health.

## Related Documents

- [Safety and Ethics Overview](Safety%20and%20Ethics%20Overview.md) — Ethical framework and hard lines
- [Privacy and Data Sovereignty](Privacy%20and%20Data%20Sovereignty.md) — Data handling details
- [Mission and Values](../project/Mission%20and%20Values.md) — Why these guardrails exist
