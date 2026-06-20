# Frequently Asked Questions

## General

### What is HelperWatch?

HelperWatch is an open-source cognitive scaffolding system for severely impacted special needs children. It uses a smartwatch, room-scanner nodes, and a secure Cloud Backend to provide real-time, location-aware verbal cues, behavioral monitoring, and caregiver notifications — using on-device speech-to-text to protect your family's privacy.

### Who is HelperWatch designed for?

Children with severe executive dysfunction, non-verbal autism, profound ADHD, intellectual disabilities, or other conditions that require near-constant verbal prompting, monitoring, and behavioral support. It is designed to supplement — not replace — human caregiving.

### Is HelperWatch a medical device?

No. HelperWatch is an assistive technology tool, not a medical device. It does not diagnose, treat, or cure any condition. It should be used alongside professional care, not as a substitute for it.

## Privacy and Safety

### Does HelperWatch send my child's data to the cloud?

**Yes, by design.** The system utilizes a secure Cloud Backend to sync routines, coordinate AI routing, and deliver caregiver status updates. 

However, HelperWatch is designed with a strict **privacy-by-design** posture. All speech-to-text (STT) transcription occurs **directly on the smartwatch** using a lightweight local model (Moonshine). Only resulting text transcripts are sent to the cloud, meaning **raw audio never leaves the smartwatch and is never sent over the network**. Transcripts sent to the Cloud Backend are processed in-memory and deleted immediately. Caregivers also have full control to toggle historical logs off entirely.

See: [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md)

### Is audio recorded and stored?

No audio files are recorded or sent over the network. The smartwatch microphone captures audio strictly for real-time transcription on-device, and the audio buffer is purged immediately after transcription. Only text transcripts are sent to the Cloud Backend, and those are processed transiently and deleted from memory instantly unless the caregiver has enabled logging for clinical trend reporting.

### What about other people in the room?

The watch microphone captures all nearby audio, including other people's voices. To protect bystander privacy:
- Raw audio is deleted on the watch the instant transcription is complete.
- Caregivers can set up privacy zones (rooms or times where audio capture pauses).
- A quick-mute gesture on the watch pauses audio for a set duration.
- Caregivers should inform visitors that audio monitoring is active.

See: [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md)

### Can the AI say something inappropriate or dangerous to my child?

The AI operates within **deterministic guardrails** — it selects responses from a parent-approved script set rather than generating freeform language. It cannot tell your child to do anything you have not explicitly pre-approved.

See: [Risks and Mitigations](../ethics/Risks%20and%20Mitigations.md)

## Hardware

### What smartwatch do I need?

Any budget Android or WearOS smartwatch with Wi-Fi, Bluetooth, a microphone, a speaker, a heart rate sensor, and an accelerometer. Most Android-based watches from the last 5 years qualify. Refurbished devices work well.

See: [Hardware Guide](Hardware%20Guide.md)

### Does it work with Apple Watch?

Not currently. HelperWatch targets Android/WearOS devices due to their openness for background service apps and significantly lower cost for refurbished units.

### What are room scanner nodes?

Small USB-powered ESP32 development boards placed in each room of the house. The ESP32 scanner nodes actively scan for the smartwatch's BLE broadcast, measure the signal strength, and send RSSI values to the Cloud Backend over Wi-Fi. This provides instant, battery-efficient room tracking without requiring the watch to run constant, battery-draining scanning services.

See: [Indoor Positioning](../design/Indoor%20Positioning.md)

### Do I need a special computer?

No. HelperWatch runs entirely on a secure Cloud Backend. You do not need to purchase a dedicated computer, Raspberry Pi, or keep a home computer running 24/7.

See: [Hardware Guide](Hardware%20Guide.md)

### Does it need internet?

**Yes.** Since HelperWatch uses a Cloud Backend, an active internet connection is required for the smartwatch, room scanner nodes, and caregiver mobile app to sync routines, report locations, and process AI requests.

## Features

### How does room tracking work?

The smartwatch broadcasts a unique low-power BLE advertiser signal. Wall-powered ESP32 scanner nodes in each room scan for this signal, measure its RSSI (strength), and report it to the Cloud Backend over Wi-Fi. The Cloud Backend compares these reports to determine which room the child is in. This flipped architecture is highly responsive (1-3s transition delay) and doesn't drain the watch battery.

See: [Indoor Positioning](../design/Indoor%20Positioning.md)

### Will my child become dependent on the device?

HelperWatch includes a **dynamic fading** system that automatically reduces prompting frequency as the child demonstrates mastery of a routine. The goal is to build independence — a child who no longer needs the device is the ultimate success.

See: [Risks and Mitigations](../ethics/Risks%20and%20Mitigations.md)

### Can I talk to my child through the watch?

Yes. The **push-to-talk intercom** mode lets you speak a command through the caregiver mobile app. The AI integrates your message into its next cue, delivering it in the familiar AI voice. This avoids jarring your child with an unexpected voice while giving you direct control.

See: [Caregiver Mobile App](../design/Caregiver%20Mobile%20App.md)

### Can other people hear the watch talking?

By default, the system routes audio through paired wireless earbuds, keeping the assistance private and invisible. Speaker output is available but is not the recommended default.

## Cost

### How much does the full system cost?

A minimum setup (refurbished watch + ESP32 dev boards) can be assembled for approximately **$60–135 total** for initial hardware, plus a small monthly cloud subscription (typically $5-15/month) to cover secure hosting and AI API costs.

See: [Hardware Guide](Hardware%20Guide.md) for a detailed cost breakdown.

### Are there monthly fees?

**Yes.** A reasonable cloud subscription covers the cost of secure database hosting, API token consumption, and backend coordination (typically $5–15/month).

## Related Documents

- [Getting Started](Getting%20Started.md) — Setup walkthrough
- [Hardware Guide](Hardware%20Guide.md) — What to buy and where
- [Safety and Ethics Overview](../ethics/Safety%20and%20Ethics%20Overview.md) — Ethical framework
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Data handling details

