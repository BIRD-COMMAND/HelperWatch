# Safety and Ethics Overview

## The Ethical Mirror

A wearable device that constantly monitors a child's location down to the room, listens to their environment, records their audio, and feeds it into an artificial intelligence to analyze behavior and issue verbal directives is, architecturally, **the exact blueprint of a surveillance system.**

If built carelessly, or for the wrong reasons, it is a digital leash.

HelperWatch exists because the same architecture — when built through the lens of open-source accessibility, fierce data privacy, and genuine care for severely impacted special needs children — transforms from a tool of surveillance into a tool of **cognitive scaffolding and radical independence.**

This document establishes the ethical framework that governs every design decision in the project.

## Guiding Principle

> Technology can augment care, but it must never replace raw human presence. HelperWatch is a supplementary tool — designed to catch the gaps when a parent is cooking dinner, sleeping, or managing other children. It is not a substitute for parenting, professional therapy, or medical care.

## Non-Negotiable Ethical Requirements

These are hard lines. They are not features to be toggled or negotiated. Any contribution, fork, or deployment that violates these requirements is considered a misuse of the project.

### 1. Data Sovereignty & Minimization

To protect the family's privacy, audio captured by the watch is streamed encrypted (TLS) to the Cloud Backend for transcription using a managed STT API (Whisper via Groq). Raw audio is processed in-memory and deleted immediately after transcription — never stored at rest. All cloud processing is transient, and no data is stored permanently on our servers unless explicitly opted into by the caregiver.

See: [Privacy and Data Sovereignty](Privacy%20and%20Data%20Sovereignty.md)

### 2. Deterministic Guardrails

The AI acts as a router for parent-approved scripts rather than having full creative freedom. This prevents unsafe hallucinations, inappropriate instructions, and unpredictable language that could confuse or distress a literal-minded child.

### 3. Bystander Privacy Protection

Raw audio files are deleted immediately after transcription. Privacy zones are easy for caregivers to toggle. The system must respect the privacy of siblings, visitors, and anyone else captured by the watch microphone.

See: [Privacy and Data Sovereignty](Privacy%20and%20Data%20Sovereignty.md)

### 4. Dynamic Fading

The system must be designed to reduce its own involvement over time. As a child demonstrates mastery of a routine, prompting frequency decreases automatically. The goal is to build independence, not dependency. A child who no longer needs the device is the ultimate success.

### 5. Transparency and Education

Public-facing documentation of all risks, mitigations, and design trade-offs is a core deliverable of the project — not an afterthought. Caregivers must understand what the system does, how it works, and what its limitations are before they deploy it.

## Known Risks

Every identified risk is documented with its mitigation status in [Risks and Mitigations](Risks%20and%20Mitigations.md). Risks fall into three categories:

1. **Fully mitigatable** — Addressed through architecture and design (e.g., encrypted transit, transient cloud memory processing, and immediate audio deletion neutralize raw audio leakage risk).
2. **Partially mitigatable** — Reduced through design but requiring caregiver awareness (e.g., bystander privacy, stigma).
3. **Design-philosophy-only** — Cannot be eliminated through code; mitigated through education, documentation, and intended-use framing (e.g., loss of human intuition, over-reliance).

## The Open-Source Commitment

By building HelperWatch as an open-source project:

- No single corporation owns the rights to a disabled child's autonomy.
- The code is auditable by anyone — parents, educators, security researchers, and advocacy organizations.
- The community can extend the system (e.g., picture schedule extensions, pacing detection optimizations) while maintaining the ethical guardrails.
- Privacy claims are verifiable, not just marketing language.

## Related Documents

- [Privacy and Data Sovereignty](Privacy%20and%20Data%20Sovereignty.md) — Detailed data handling rules
- [Risks and Mitigations](Risks%20and%20Mitigations.md) — Comprehensive risk register
- [Mission and Values](../project/Mission%20and%20Values.md) — Core project principles
- [Contributing](../project/Contributing.md) — Ethical standards for contributors
