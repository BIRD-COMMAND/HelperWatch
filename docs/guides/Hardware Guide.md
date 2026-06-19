# Hardware Guide

This guide covers all hardware needed to run HelperWatch, with recommendations and cost estimates at each price point.

## Total System Cost Estimate

| Configuration | Estimated Cost |
|---------------|---------------|
| Budget (ESP32 beacons + used watch + existing PC) | $55 – $100 |
| Standard (commercial beacons + mid-range watch + existing PC) | $100 – $180 |
| Dedicated hub (add a Raspberry Pi 5) | +$35 |

## Smartwatch

### Requirements

The HelperWatch wearable app requires an Android or WearOS smartwatch with:

| Feature | Required | Notes |
|---------|----------|-------|
| Wi-Fi | Yes | For communication with the local server |
| Bluetooth Low Energy | Yes | For scanning room beacons |
| Microphone | Yes | For audio capture |
| Speaker | Yes | For verbal cue playback (earbuds are preferred but speaker is fallback) |
| Heart rate sensor | Yes | Optical PPG sensor for biometric monitoring |
| Accelerometer | Yes | For motion pattern detection |
| Bluetooth audio output | Recommended | For pairing with wireless earbuds (earbud-first default) |

### Recommended Devices

<!-- TODO: Specific model recommendations and purchasing links will be updated as device compatibility testing progresses. -->

**Budget tier ($35–50):**
- Older refurbished Samsung Galaxy watches
- Generic standalone Android smartwatches (often marketed as kids' watches)
- Budget fitness watches running full or Go-edition Android

**Mid-range tier ($50–80):**
- Samsung Galaxy Watch Active2 (refurbished)
- Mid-range WearOS devices from major manufacturers

**What to avoid:**
- Watches without Wi-Fi (Bluetooth-only fitness bands)
- Proprietary OS watches that do not support Android app sideloading (e.g., Apple Watch, basic Fitbit models)

### Wireless Earbuds

Any standard Bluetooth earbuds compatible with the smartwatch. The system defaults to earbud output for privacy and stigma reduction. Budget Bluetooth earbuds ($10–20) are sufficient.

## Room Beacons

HelperWatch uses BLE (Bluetooth Low Energy) beacons placed in each room to track the child's location. A typical home needs **4–6 beacons**.

### Option A: ESP32 Development Boards (~$3–4 each)

The most affordable option. These are small microcontroller boards that are flashed with a simple beacon script and plugged into USB wall chargers.

| Detail | Specification |
|--------|--------------|
| **Cost** | ~$3–4 per unit |
| **Power** | USB wall charger (continuous power, no batteries) |
| **Setup** | One-time firmware flash using provided tool |
| **Recommended boards** | ESP32-C3, ESP32-S3, or standard ESP32 dev boards |
| **Where to buy** | Amazon, AliExpress |

**Pros:** Extremely cheap, always powered, firmware-updatable.

**Cons:** Requires a one-time flash step (instructions provided). Slightly larger than commercial tags.

### Option B: Commercial BLE Beacon Tags (~$10–15 each)

Off-the-shelf beacon devices that work out of the box.

| Detail | Specification |
|--------|--------------|
| **Cost** | ~$10–15 per unit |
| **Power** | Coin-cell battery (1+ year lifespan) |
| **Setup** | None — activate and place |
| **Compatible standards** | iBeacon, Eddystone |
| **Recommended brands** | Feasycom, RadBeacon, or generic iBeacon tags |
| **Where to buy** | Amazon |

**Pros:** Zero configuration, very small and discreet, long battery life.

**Cons:** More expensive, batteries eventually need replacement, less flexibility.

### Placement Tips

- Place beacons near the center of each room, at mid-height (shelf, counter, or wall-mounted).
- Avoid placing directly on or behind large metal objects (refrigerators, filing cabinets).
- Avoid placement directly behind large mirrors (signal reflection can cause inaccuracy).
- For multi-floor homes, consider additional beacons to resolve above/below ambiguity.

See: [Indoor Positioning](../design/Indoor%20Positioning.md) for detailed placement guidance and calibration.

## Server Hardware (Local AI Hub)

The HelperWatch server hub runs on the family's existing computer. The table below shows expected performance by hardware tier.

| Hardware | Speech-to-Text | AI Model Size | Response Quality |
|----------|---------------|---------------|-----------------|
| **Modern Mac** (M1, M2, M3, M4 Apple Silicon) | Real-time | 8B parameters (Llama-3, Qwen3-8B) | Flawless — sub-second cue generation |
| **Standard Windows/Linux PC** (Intel i5/i7, AMD Ryzen 5/7, no dedicated GPU) | Near real-time | 3B parameters (Phi-3, Qwen3-3B) | Good — adequate for all prompting |
| **Budget PC** (older Intel i3, entry-level AMD, 8GB RAM) | ~1–2 second delay | 1.5B parameters | Functional — suitable for scheduled routines |
| **Raspberry Pi 5** (~$35 dedicated hub) | ~1–2 second delay | 1.5B parameters | Functional — dedicated always-on option |

### Minimum Specifications

<!-- TODO: Exact minimum specs will be validated during Phase 2 prototyping. -->

- **CPU:** Any x86-64 or ARM64 processor from the last ~5 years
- **RAM:** 8 GB minimum (16 GB recommended for 8B models)
- **Storage:** ~5 GB free for the application and AI models
- **OS:** Windows 10+, macOS 12+, or Linux (Ubuntu 22.04+ or equivalent)
- **Network:** Connected to the same Wi-Fi network as the watch

### Raspberry Pi 5 as a Dedicated Hub

For families who don't want to leave a PC running, a Raspberry Pi 5 ($35) can serve as a dedicated, always-on server. It plugs into the home router via Ethernet and runs quietly with minimal power consumption.

**Limitations:** The Pi 5 can run smaller AI models (1.5B parameters) with acceptable latency for routine-based prompting. It is not suitable for real-time conversational AI processing.

## Summary: Shopping List

### Minimum viable setup:

- 1x budget Android smartwatch (refurbished): ~$40
- 4x ESP32 dev boards: ~$15
- 4x USB wall chargers (if not already owned): ~$10
- Existing home computer: $0
- Existing smartphone: $0

**Total: ~$65**

## Related Documents

- [Getting Started](Getting%20Started.md) — Setup walkthrough
- [Indoor Positioning](../design/Indoor%20Positioning.md) — Beacon technology details
- [Local Server Hub](../design/Local%20Server%20Hub.md) — Server software and performance
- [System Architecture](../design/System%20Architecture.md) — How all components connect
