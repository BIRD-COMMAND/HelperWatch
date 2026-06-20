# Hardware Guide

This guide covers all hardware needed to deploy HelperWatch in a home, with recommendations and cost estimates.

## Total System Cost Estimate

| Component | Cost Type | Estimated Cost |
|-----------|-----------|----------------|
| **Smartwatch** (Refurbished WearOS) | One-time | $35 – $100 |
| **Room Scanner Nodes** (4-6 ESP32 boards) | One-time | $15 – $25 |
| **USB Wall Chargers** (if not owned) | One-time | $10 |
| **Cloud Hosting & AI Tokens** | Recurring | $5 – $15 / month |
| **Total Initial Hardware Cost** | **One-time** | **$60 – $135** |

## Smartwatch

### Requirements

The HelperWatch wearable app requires a budget Android or WearOS smartwatch with:

> **Battery life expectation:** By keeping the watch's role to BLE advertising, audio streaming, and sensor telemetry — with no on-device inference — we expect **12–16+ hours of battery life** under typical caregiving conditions. See [Wearable App — Battery](../design/Wearable%20App.md) for details.

| Feature | Required | Notes |
|---------|----------|-------|
| Wi-Fi | Yes | For direct connection to the Cloud Backend |
| Bluetooth Low Energy | Yes | For broadcasting its BLE advertising ID |
| Microphone | Yes | For voice capture (streamed to Cloud Backend for STT) |
| Speaker | Yes | For verbal cue playback |
| Heart rate sensor | Yes | Optical PPG sensor for biometric monitoring |
| Accelerometer | Yes | For motion pattern detection (pacing, walking) |
| Bluetooth audio output | Recommended | For pairing with wireless earbuds (earbud-first default) |

### Recommended Devices

**Budget tier ($35–50):**
- Older refurbished Samsung Galaxy watches (WearOS compatible models)
- Refurbished WearOS devices from Motorola, Fossil, or Mobvoi (TicWatch)

**Mid-range tier ($50–80):**
- Refurbished Samsung Galaxy Watch 4 or 5
- Newer WearOS smartwatches from major brands

**What to avoid:**
- Watches without Wi-Fi (Bluetooth-only fitness trackers)
- Smartwatches running proprietary operating systems that do not support custom WearOS/Android apps (e.g., Apple Watch, proprietary Fitbit models, Garmin watches)

### Wireless Earbuds

Any standard Bluetooth earbuds compatible with the smartwatch. The system defaults to earbud output for privacy and stigma reduction. Budget Bluetooth earbuds ($10–20) are sufficient.

---

## Room Scanner Nodes

HelperWatch uses wall-powered ESP32 development boards placed in each room to scan for the child's watch and report signal strengths. A typical home needs **4–6 scanner nodes** (one per room).

### ESP32 Development Boards (~$3–4 each)

These are small microcontrollers that plug into standard USB wall outlets and connect to your home Wi-Fi.

| Detail | Specification |
|--------|--------------|
| **Cost** | ~$3–4 per unit |
| **Power** | USB wall charger (continuous power, no batteries) |
| **Setup** | One-time firmware flash using a web browser tool |
| **Recommended boards** | ESP32-C3, ESP32-S3, or standard ESP32 development boards |
| **Where to buy** | Amazon, AliExpress |

**Pros:** Extremely cheap, continuously powered, firmware-updatable, scans continuously (100% duty cycle) for fast transition detection.

**Cons:** Requires a one-time setup step to flash the firmware.

*Note: Commercial BLE beacon tags (such as coin-cell iBeacons) cannot be used in this architecture because they are broadcast-only and cannot scan for the watch or connect to Wi-Fi.*

### Placement Tips

- Plug nodes into wall outlets located near the center of each room, at mid-height (on a shelf, counter, or wall outlet).
- Avoid placing directly behind large metal objects (refrigerators, metal cabinets) that block Bluetooth signals.
- Avoid placement directly behind large mirrors (signal reflection can cause inaccuracy).
- For multi-floor homes, place nodes strategically to maximize distance between vertical lines to resolve above/below floor ambiguity.

See: [Indoor Positioning](../design/Indoor%20Positioning.md) for detailed placement guidance and calibration.

---

## Cloud Backend Service

HelperWatch runs its AI processing and orchestration in a secure cloud environment. **No home computer, desktop application, or local server (like a Raspberry Pi) is required.**

### Benefits of Cloud-First:
- **No hardware maintenance:** Caregivers do not need to keep a home computer running 24/7 or manage software installations.
- **Zero local network setup:** No local service discovery or peer-to-peer remote access tunnels required.
- **Sub-second response time:** Cues are classified and routed on high-performance cloud hardware instantly.

### Ongoing Cloud Costs:

| Component | Estimated Monthly Cost |
|-----------|----------------------|
| Cloud hosting (small container, always-on for WSS) | $5–7 |
| Managed PostgreSQL (small instance) | $0–5 (free tier on Supabase/Neon) |
| LLM API tokens (Groq, classifier-only usage) | $1–3 |
| Push notifications (FCM/APNs) | Free |
| **Total** | **$6–15** |

---

## Summary: Shopping List

### Minimum viable setup:

- 1x budget Android/WearOS smartwatch (refurbished): ~$40
- 4x ESP32 dev boards: ~$15
- 4x USB wall chargers (if not already owned): ~$10
- HelperWatch Cloud account: ~$5-15/month

**Total initial hardware cost: ~$65**

---

## Related Documents

- [Getting Started](Getting%20Started.md) — Setup walkthrough
- [Indoor Positioning](../design/Indoor%20Positioning.md) — Indoor positioning logic and setup
- [Cloud Backend](../design/Cloud%20Backend.md) — Cloud services details
- [System Architecture](../design/System%20Architecture.md) — Technical component overview
