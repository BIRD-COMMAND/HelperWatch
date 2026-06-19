# Indoor Positioning

## The Problem with GPS Indoors

Standard GPS does not work reliably indoors due to signal attenuation from walls, roofs, and furniture (Ozsoy et al., 2013). Even when a GPS signal is received indoors, standard accuracy ranges from 1.5–5 meters outdoors and drifts significantly inside a building. This makes GPS completely incapable of distinguishing between adjacent rooms in a standard house.

HelperWatch requires **>90% room-level accuracy** to provide contextually appropriate prompts (e.g., knowing the child is in the bathroom to trigger the hygiene routine, not the bedroom routine).

## Solution: BLE Beacon Mesh

Instead of GPS, HelperWatch uses a network of small Bluetooth Low Energy (BLE) beacon devices placed throughout the home — one per room. The child's smartwatch continuously scans for these beacon signals, and the local server uses signal strength data to determine which room the child is in.

## How It Works

### Room Fingerprinting via RSSI

Each beacon continuously broadcasts a unique identifier. The watch detects these broadcasts and measures the **RSSI (Received Signal Strength Indicator)** for each beacon.

- When the child is in the kitchen, the kitchen beacon's RSSI is strongest.
- As they walk into the bedroom, the bedroom beacon's signal strengthens and the kitchen beacon's fades.
- The local server compares the current RSSI profile against known room signatures to determine location.

This approach — known as RSSI fingerprinting — has been demonstrated to achieve >90% room-level accuracy in standard residential environments (Kim et al., 2021).

### Fingerprinting Calibration

During initial setup, the caregiver performs a brief room calibration:

1. Walk to each room with the watch and tap "I'm in the Kitchen" (or equivalent) in the setup app.
2. The system records the BLE signal profile for that room.
3. Repeat for each room.

This creates a baseline signal map that the system uses for ongoing room identification.

<!-- TODO: Adaptive fingerprinting (automatic re-calibration as signal conditions change) will be explored during Phase 1 prototyping. -->

## Beacon Hardware Options

### Option A: ESP32 Development Boards (~$3–4 each)

Raw ESP32 or ESP32-C3 development boards purchased from Amazon or AliExpress. No soldering required.

**Setup:**
1. Plug the board into a standard USB wall charger in each room.
2. Flash it with a small, static open-source script that continuously broadcasts a unique BLE beacon ID.

**Pros:**
- Extremely cheap.
- Powered continuously via USB (no battery replacement).
- Can be reflashed with updated firmware if the beacon protocol evolves.

**Cons:**
- Requires a one-time firmware flash (instructions provided in the setup guide).
- Slightly larger than commercial beacon tags.

### Option B: Commercial BLE Beacon Tags (~$10–15 each)

Off-the-shelf, battery-powered BLE beacon devices (e.g., Feasycom, RadBeacon, or generic iBeacon-compatible tags).

**Setup:**
1. Activate the beacon (some are always-on out of the box).
2. Stick it to the wall or a shelf in each room.

**Pros:**
- Zero configuration. No flashing, no USB.
- Very small and discreet.
- Coin-cell battery lasts 1+ years.

**Cons:**
- More expensive per unit.
- Batteries eventually need replacement.
- Less flexibility if firmware changes are needed.

## Typical Home Deployment

| Room | Beacon Placement | Notes |
|------|-----------------|-------|
| Kitchen | On the refrigerator or a cabinet | Central to the room, away from metal appliances that might interfere |
| Bedroom | On a bookshelf or nightstand | Near the center of the room at mid-height |
| Bathroom | On a shelf or mounted to the wall | Avoid placement near large mirrors (signal reflection) |
| Living Room | On a media console or shelf | Central location |
| Hallway / Transition Zone | Optional; near doorways | Helps detect room transitions faster |

A typical home requires **4–6 beacons** for reliable coverage.

## Accuracy Considerations

- **Wall material matters.** Drywall is nearly transparent to BLE; concrete and metal significantly attenuate signals. Homes with unusual construction may require additional calibration or beacon density.
- **Interference.** Other Bluetooth devices (speakers, headphones, smart home devices) can introduce noise. The fingerprinting algorithm should use a rolling average of RSSI values rather than instantaneous readings.
- **Multi-floor homes.** BLE signals penetrate floors, which can cause ambiguity between rooms directly above/below each other. Additional beacons or floor-specific calibration may be needed.

## References

- Kim, T., Kim, K.-S., & Li, K.-J. (2021). Improving Room-Level Location for Indoor Trajectory Tracking with Low IPS Accuracy. *ISPRS International Journal of Geo-Information*, *10*(9), 620.
- Ozsoy, K., Bozkurt, A., & Tekin, I. (2013). Indoor positioning based on global positioning system signals. *Microwave and Optical Technology Letters*, *55*(5), 1091–1097.

## Related Documents

- [System Architecture](System%20Architecture.md) — How positioning fits into the system
- [Wearable App](Wearable%20App.md) — The watch-side BLE scanning implementation
- [Hardware Guide](../guides/Hardware%20Guide.md) — Beacon purchasing recommendations
