# Indoor Positioning

## The Problem with GPS Indoors

Standard GPS does not work reliably indoors due to signal attenuation from walls, roofs, and furniture (Ozsoy et al., 2013). Even when a GPS signal is received indoors, standard accuracy ranges from 1.5–5 meters outdoors and drifts significantly inside a building. This makes GPS completely incapable of distinguishing between adjacent rooms in a standard house.

HelperWatch requires **>90% room-level accuracy** to provide contextually appropriate prompts (e.g., knowing the child is in the bathroom to trigger the hygiene routine, not the bedroom routine).

## Solution: Flipped BLE Room Scanner Mesh

To achieve reliable room-level accuracy without overloading the smartwatch's battery or fighting WearOS background scanning limitations, HelperWatch uses a **flipped positioning architecture**:

- **Smartwatch as Advertiser:** The child's smartwatch acts as a passive BLE broadcaster, advertising a unique, secure BLE identifier. WearOS natively handles background BLE advertising with negligible power consumption.
- **ESP32 Boards as Active Scanners:** Inexpensive, wall-powered ESP32 microcontroller boards are plugged into outlets in each room. They scan continuously for the watch's BLE signal, measure the signal strength, and report RSSI values via Wi-Fi to the Cloud Backend.
- **Cloud Backend as Resolver:** The Cloud Backend collects RSSI values from all active scanner nodes and determines which room the child is in.

```
  ┌──────────────┐
  │  Smartwatch  │ (Passive BLE Advertising)
  └──────┬───────┘
         │
         │ BLE Broadcast Signal
         ▼
  ┌──────────────┐  -60 dBm   ┌────────────────┐
  │ ESP32 Node   ├───────────►│                │
  │  (Kitchen)   │            │                │
  └──────────────┘            │                │
                              │ Cloud Backend  │ (RSSI Comparison)
  ┌──────────────┐  -85 dBm   │                │
  │ ESP32 Node   ├───────────►│                │
  │  (Bedroom)   │            │                │
  └──────────────┘            └────────────────┘
```

## How It Works

### Room Fingerprinting via RSSI

Each ESP32 node continuously scans for the watch's specific advertisement. When it receives a packet, it measures the **RSSI (Received Signal Strength Indicator)**.

- When the child is in the kitchen, the kitchen ESP32 reports the strongest RSSI (e.g., -55 dBm).
- The bedroom ESP32 might still detect the watch but will report a much weaker RSSI (e.g., -85 dBm).
- The Cloud Backend compares the reports in real-time. The room with the strongest running-average RSSI is resolved as the child's active room.

This RSSI fingerprinting approach achieves >90% room-level accuracy in standard residential environments (Kim et al., 2021).

### Fingerprinting Calibration

During initial setup, the caregiver performs a brief room calibration:

1. Stand in a room with the child's watch.
2. In the caregiver mobile app, tap **"Calibrate [Room Name]"** (e.g., "Kitchen").
3. The Cloud Backend records the RSSI profiles received by all ESP32 scanner nodes for 10–15 seconds to create a baseline signature.
4. Repeat for each room.

This creates a baseline signal map that the backend uses for ongoing room identification.

Adaptive fingerprinting (automatic re-calibration as signal conditions change over time) is a development goal. Until implemented, caregivers may need to re-run calibration after significant furniture rearrangements or seasonal changes that affect signal propagation.

## Scanner Node Hardware: ESP32 Boards (~$3–4 each)

HelperWatch uses standard, off-the-shelf ESP32 or ESP32-C3 development boards (e.g., NodeMCU style) plugged into USB chargers.

**Setup:**
1. Plug the ESP32 board into a standard USB wall charger in each room.
2. Flash the board with the HelperWatch scanner firmware using a web-based flashing tool (WebUSB/WebSerial). During flashing, the tool writes the home Wi-Fi credentials and a per-account API key into the firmware.
3. On boot, the ESP32 connects to Wi-Fi and authenticates with the Cloud Backend using its API key to register under the caregiver's account.

**Pros:**
- Extremely cheap (~$3–4 per room).
- Wall-powered (no battery replacements or maintenance).
- Scans continuously (100% duty cycle) for instant transition detection.
- Firmware-updatable over-the-air (OTA): each node checks for updates on boot and on a daily schedule, downloading signed firmware binaries from the Cloud Backend (see [Cloud Backend — OTA](Cloud%20Backend.md)).

Because commercial BLE tags can only broadcast and cannot scan or connect to Wi-Fi, they are not compatible with this flipped architecture. The ESP32 scanner node is the required positioning hardware.

## Typical Home Deployment

| Room | Node Placement | Notes |
|------|----------------|-------|
| Kitchen | Plugged into a counter outlet | Central to the room, away from major metal appliances |
| Bedroom | Plugged into an outlet near the bed | Near the center of the room, mid-height shelf is ideal |
| Bathroom | Plugged into a GFCI outlet | Avoid placement directly behind large mirrors |
| Living Room | Plugged near the media console | Central location with clear line-of-sight |
| Hallway / Transition Zone | Optional; near doorways | Helps detect room transitions faster |

A typical home requires **4–6 scanner nodes** for reliable coverage.

## Accuracy Considerations

- **Wall material matters.** Drywall is nearly transparent to BLE; concrete, brick, and metal significantly attenuate signals. Homes with heavy construction may require additional nodes or custom calibration.
- **Wi-Fi Coverage.** Each ESP32 scanner node requires a stable connection to the home Wi-Fi to report RSSI values. Rooms with weak Wi-Fi signal might experience delayed or lost location reports.
- **Latency.** Since the ESP32 scanner nodes are wall-powered, they scan continuously and report RSSI values multiple times per second. Room transitions are resolved on the Cloud Backend within **1–3 seconds**, providing a highly responsive system.
- **Multi-floor homes.** BLE signals penetrate floors, which can cause ambiguity between rooms directly above/below each other. Multi-floor homes need nodes placed strategically to maximize distance between vertical lines and floor-specific calibration.

## Security

Each ESP32 scanner node authenticates with the Cloud Backend using a **per-account API key** burned into the firmware during the WebUSB provisioning step. The Cloud Backend validates this key on every connection to ensure only authorized nodes can report RSSI data to a caregiver's account. API keys can be revoked by the caregiver via the mobile app if a node is lost or compromised.

## References

- Kim, T., Kim, K.-S., & Li, K.-J. (2021). Improving Room-Level Location for Indoor Trajectory Tracking with Low IPS Accuracy. *ISPRS International Journal of Geo-Information*, *10*(9), 620.
- Ozsoy, K., Bozkurt, A., & Tekin, I. (2013). Indoor positioning based on global positioning system signals. *Microwave and Optical Technology Letters*, *55*(5), 1091–1097.

## Related Documents

- [System Architecture](System%20Architecture.md) — How positioning fits into the system
- [Wearable App](Wearable%20App.md) — Smartwatch BLE advertising details
- [Cloud Backend](Cloud%20Backend.md) — The backend resolving location telemetry
- [Hardware Guide](../guides/Hardware%20Guide.md) — ESP32 purchasing recommendations
