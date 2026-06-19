# Frequently Asked Questions

## General

### What is HelperWatch?

HelperWatch is an open-source cognitive scaffolding system for severely impacted special needs children. It uses a smartwatch, room-tracking beacons, and a local AI server to provide real-time, location-aware verbal cues, behavioral monitoring, and caregiver notifications — all processed locally on the family's own hardware.

### Who is HelperWatch designed for?

Children with severe executive dysfunction, non-verbal autism, profound ADHD, intellectual disabilities, or other conditions that require near-constant verbal prompting, monitoring, and behavioral support. It is designed to supplement — not replace — human caregiving.

### Is HelperWatch a medical device?

No. HelperWatch is an assistive technology tool, not a medical device. It does not diagnose, treat, or cure any condition. It should be used alongside professional care, not as a substitute for it.

## Privacy and Safety

### Does HelperWatch send my child's data to the cloud?

**No, by default.** All audio, biometric, and location data is processed entirely on your home computer. Nothing leaves your home network unless you explicitly choose to enable the cloud API fallback (for families with underpowered computers).

See: [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md)

### Is audio recorded and stored?

Audio is captured temporarily for real-time transcription only. Raw audio is **deleted immediately** after it is converted to text. No audio files are saved on any device. Only text transcripts (if logging is enabled) are retained locally.

### What about other people in the room?

The watch microphone captures all nearby audio, including other people's voices. To protect bystander privacy:

- Raw audio is deleted the instant transcription is complete.
- Caregivers can set up privacy zones (rooms or times where audio capture pauses).
- A quick-mute gesture on the watch pauses audio for a set duration.
- Caregivers should inform visitors that audio monitoring is active.

See: [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md)

### Can the AI say something inappropriate or dangerous to my child?

The AI operates within **deterministic guardrails** — it selects responses from a parent-approved script set rather than generating freeform language. It cannot tell your child to do anything you have not explicitly pre-approved.

See: [Risks and Mitigations](../ethics/Risks%20and%20Mitigations.md)

## Hardware

### What smartwatch do I need?

Any budget Android or WearOS smartwatch with Wi-Fi, Bluetooth, a microphone, a speaker, a heart rate sensor, and an accelerometer. Most Android-based watches from the last 5 years qualify. Older refurbished devices ($35–80) work well.

See: [Hardware Guide](Hardware%20Guide.md)

### Does it work with Apple Watch?

Not currently. HelperWatch targets Android/WearOS devices due to their openness for background service apps and significantly lower cost for refurbished units.

### What are room beacons?

Small Bluetooth devices placed in each room that broadcast a unique signal. The watch detects which beacon is closest to determine the child's location. You need one per room (typically 4–6). Options range from $3 ESP32 boards to $15 commercial tags.

See: [Indoor Positioning](../design/Indoor%20Positioning.md)

### Do I need a special computer?

No. HelperWatch runs on most home computers from the last ~5 years. Modern Macs provide the best performance, but standard Windows and Linux PCs work well. You can also use a Raspberry Pi 5 (~$35) as a dedicated hub.

See: [Hardware Guide](Hardware%20Guide.md)

### Does it need internet?

**No.** The core system runs entirely on your local home Wi-Fi network with no internet connection required. Internet is only needed if you choose to use the optional cloud API fallback.

## Features

### How does room tracking work?

The smartwatch scans for Bluetooth signals from beacons placed in each room. By comparing signal strengths, the system determines which room the child is in with >90% accuracy. This replaces GPS, which does not work reliably indoors.

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

A minimum setup (refurbished watch + ESP32 beacons + existing home computer) can be assembled for approximately **$55–100 total.** There are no subscription fees for core functionality.

See: [Hardware Guide](Hardware%20Guide.md) for a detailed cost breakdown.

### Are there monthly fees?

**No.** The core system is free and runs on your local hardware. The only potential recurring cost is if you opt into the cloud API fallback, where you pay the API provider directly (typically pennies per day for light usage).

## Related Documents

- [Getting Started](Getting%20Started.md) — Setup walkthrough
- [Hardware Guide](Hardware%20Guide.md) — What to buy and where
- [Safety and Ethics Overview](../ethics/Safety%20and%20Ethics%20Overview.md) — Ethical framework
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — Data handling details
