# Tech Stack Review

An honest assessment of every technology choice currently named in the HelperWatch documentation. Organized by component.

---

## 1. Wearable App — Watch Platform

### What the docs say
Target "any budget Android or WearOS smartwatch" from the last 5 years. Write a background service in Kotlin (discussion log) or React Native (architecture doc). Continuously scan BLE beacons, capture audio, sample biometrics, and transmit over Wi-Fi.

### Verdict: The right *idea*, with misleading details

**The platform choice (Android/WearOS) is correct.** It is the only wearable ecosystem that permits sideloaded background services with raw sensor access. No alternative exists at this price point.

**But the docs significantly understate the difficulty of persistent background operation on WearOS.** This is the single biggest technical risk in the project.

#### Gotcha: Background BLE Scanning Is Severely Throttled

WearOS aggressively kills or suspends background BLE scans **within 4–5 minutes** of the screen dimming. This is not a bug — it is a deliberate power management policy. Even Foreground Services face strict restrictions unless started in direct response to a user-initiated event.

**What this means for HelperWatch:**
- You cannot simply run a continuous BLE scan loop. The OS will kill it.
- You must use `PendingIntent`-based scanning (the OS wakes your app only when a matching beacon is found) or intermittent scan windows (e.g., 12 seconds per minute).
- Battery life with continuous BLE + audio + Wi-Fi transmission on a budget watch will likely be **4–6 hours**, not a full day. This needs to be stated honestly in the hardware guide.
- "Silent failures" are common — the scan appears to run in logs but the BLE hardware has actually been reclaimed by the OS.

**Recommendation:** Keep the platform. Add a dedicated section to the Wearable App doc on power management strategy. Budget watch battery life should be framed honestly: this will likely need mid-day charging. Consider whether the watch can offload some BLE scanning to a paired phone via `CompanionDeviceManager`.

---

## 2. Wearable App — React Native on WearOS

### What the docs say
"WearOS can run React Native apps" and "you can share a significant portion of your data-handling, state-management, and local networking code between the watch app and the parent's mobile app."

### Verdict: Wrong. This claim is misleading.

**React Native has no official support for WearOS.** There is no first-party SDK, no official target, and no maintained community bridge for WearOS UI components. The few community projects that exist (e.g., `react-native-wear-connectivity`) only handle phone-to-watch *data sync*, not rendering a UI on the watch.

The standard practice — and the only robust path — is:

- **Watch app:** Native Kotlin + Jetpack Compose for Wear OS.
- **Mobile app:** React Native (or whatever cross-platform framework you choose).
- **Shared logic:** If desired, extract pure business logic (e.g., data models, protocol handling) into a Kotlin Multiplatform (KMP) library that both apps consume.

**Recommendation:** All docs should be corrected to state the watch app will be native Kotlin. The "shared codebase cuts development in half" claim should be removed — it is false for this platform combination. Code sharing is possible at the logic layer via KMP, but the UI and sensor-access layers are entirely separate.

---

## 3. Indoor Positioning — BLE Beacons (ESP32 / Commercial)

### What the docs say
Place ESP32 boards or commercial BLE tags in each room. Use RSSI fingerprinting for >90% room-level accuracy.

### Verdict: Correct approach. Realistic claims.

This is the standard, well-proven method for room-level indoor positioning. The technology choices are appropriate:

- **ESP32 as beacons:** Legitimate. An ESP32-C3 running a static BLE advertising script is a $3 beacon. The firmware is trivial (~50 lines of Arduino code).
- **Commercial BLE tags:** Legitimate alternative for non-technical families.
- **RSSI fingerprinting for >90% room accuracy:** Achievable in most standard homes. The cited research (Kim et al., 2021) supports this.

#### Gotchas

- **Calibration is fragile.** Furniture changes, door open/closed states, and even humidity shifts alter RSSI profiles. The docs mention calibration but understate how often re-calibration may be needed. An adaptive fingerprinting approach (continuous learning) should be a Phase 1 goal, not a TODO.
- **Multi-floor ambiguity is real.** BLE passes through floors easily. A kitchen beacon directly below a bedroom will cause false positives. The docs mention this but should be more emphatic: multi-floor homes need significantly more beacons and may need floor-specific calibration.
- **The watch BLE throttling problem (above) directly impacts this.** If scans only happen 12 seconds per minute, room transitions will be detected with a delay of up to ~50 seconds in the worst case. This is probably acceptable for the use case, but should be documented.

**Recommendation:** Keep. Add adaptive fingerprinting to the roadmap as a Phase 1 deliverable, not a vague TODO. Be more direct about multi-floor challenges.

---

## 4. Local Server Hub — Desktop App Framework (Electron / Tauri)

### What the docs say
Package the backend as a double-clickable desktop app using Electron or Tauri.

### Verdict: Right idea. Tauri is the better choice — but with caveats.

Both are viable. The distinction matters:

| Factor | Electron | Tauri |
|--------|----------|-------|
| Bundle size | ~100MB+ (ships Chromium) | ~3–15MB (uses OS webview) |
| RAM usage | High (multiple Chromium processes) | Low |
| Backend language | Node.js (JS/TS) | Rust |
| Maturity | Very mature, huge ecosystem | Mature, growing fast |

**Since the app will also be running Ollama + STT models that consume significant RAM and CPU, Tauri's lower overhead is a meaningful advantage.** You don't want Electron eating 300MB of RAM while a 3B model is trying to use the same machine.

#### Gotcha: "Bundling" Ollama Is Not Simple

The docs imply Ollama runs as a "hidden, bundled background service inside your app." In practice:

- Most projects use a **dependency model**: check if Ollama is installed at launch, guide the user to install it if not. Literally bundling the Ollama binary inside your installer creates issues with updates, antivirus false positives, and OS permission models.
- Tauri's "sidecar" feature can package the Ollama binary, but you're then responsible for managing its lifecycle, updates, and model downloads — which is a significant engineering effort.
- Model files are **large** (1.5B = ~1GB, 3B = ~2GB, 8B = ~4.5GB). The auto-benchmark-and-download flow described in the docs is correct in concept, but the UX of a first-run 2GB download needs careful handling.

**Recommendation:** Keep Tauri as the primary recommendation. Replace the "bundled inside your app" language with a more honest description: the app manages Ollama as an external service, handling installation, startup, and model downloads through a guided first-run experience. Electron should remain as an alternative for contributors more comfortable with Node.js.

---

## 5. Local Server Hub — Speech-to-Text (Whisper.cpp / Moonshine)

### What the docs say
Use Whisper.cpp or Moonshine for local, real-time STT. "They compile into a single tiny executable. They don't need Python, and they don't need a high-end gaming GPU."

### Verdict: Correct. Moonshine is the stronger choice for this project.

Both are legitimate, production-quality local STT options. The claims are accurate.

**Moonshine is the better fit** for HelperWatch specifically:

- It is purpose-built for streaming/real-time use (low latency, ~245M parameters).
- It has pre-built packages for Android, iOS, and wearables — which matters if you ever want on-watch pre-processing.
- MIT licensed.
- It matches or exceeds Whisper Large v3 accuracy on English benchmarks despite being much smaller.

Whisper.cpp is more mature and battle-tested, but it's optimized for batch transcription, not streaming. For a system that needs to react to speech in real time, Moonshine's streaming architecture is a better match.

#### Gotcha

- **English-only (for now).** The MIT-licensed Moonshine models are English-focused. Multilingual families would need Whisper or a cloud fallback.
- The claim that these run "real-time on a standard CPU" is true for short utterances but may struggle with continuous, hours-long audio streams on budget hardware. The system should be designed to process audio in windowed chunks, not as a continuous stream.

**Recommendation:** Lead with Moonshine, keep Whisper.cpp as a fallback. Note the English limitation. Ensure the architecture processes audio in short chunks rather than as a continuous stream.

---

## 6. Local Server Hub — LLM (Ollama with 1.5B–8B models)

### What the docs say
Run Llama-3 8B on modern Macs, Phi-3/Qwen3-3B on standard PCs, and 1.5B models on budget hardware or Raspberry Pi 5.

### Verdict: Correct *if* the task is narrowly constrained. The docs overstate what small models can do.

The auto-tiering approach (benchmark hardware → pick model size) is sound. The specific model names are reasonable choices for mid-2026. But:

#### Gotcha: Small Models Are Brittle at Open-Ended Conversation

1.5B and 3B models excel at **structured, verifiable tasks** (math, code, classification). They are significantly weaker at:

- Open-ended conversation
- Multi-turn context management
- Nuanced tone and emotional sensitivity
- Long-tail knowledge retrieval

**This is actually fine for HelperWatch** — because the deterministic guardrails design means the LLM is acting as a **router/classifier**, not a creative conversationalist. It receives structured context (room, time, routine step, biometric state) and selects from a constrained set of pre-approved responses. A 3B model can do this well.

**But the docs should be clearer about this.** The current language implies the LLM is generating free-form verbal cues, which creates an expectation mismatch. On a budget PC with a 1.5B model, the system should be understood as a sophisticated rule engine with some AI flexibility, not a conversational agent.

The Raspberry Pi 5 tier labeled as "functional" is honest, but barely. A 1.5B model on a Pi 5 will have noticeable latency (~3–5 seconds for generation, not "1–2 seconds" as the docs claim), and the quality of output at 1.5B is marginal for anything beyond simple template selection.

**Recommendation:** Keep the tiered approach. Correct the Pi 5 latency claims. Add language clarifying that the LLM's role is *classification and routing*, not creative generation — and that this is a feature, not a limitation, because it aligns with the deterministic guardrails safety requirement.

---

## 7. Communication — MQTT vs. WebSockets

### What the docs say
Use MQTT or WebSockets for watch-to-server and app-to-server communication.

### Verdict: Both viable. Pick one early.

Leaving this as "MQTT or WebSockets" in the architecture docs is appropriate for Phase 0, but the decision should be made in Phase 1.

| Factor | MQTT | WebSockets |
|--------|------|------------|
| Design purpose | Pub/sub messaging for constrained devices | General bidirectional streaming |
| Overhead | Very low (2-byte header) | Low (HTTP upgrade, then frames) |
| Broker required | Yes (e.g., Mosquitto) | No (direct connection) |
| Audio streaming | Not ideal (designed for small messages) | Good fit |
| Implementation complexity | Need to run a broker process | Simpler (built into most web frameworks) |

**For this project, WebSockets are likely the better single choice.** The system needs to stream compressed audio (not just small telemetry messages), and WebSockets handle that more naturally. Adding an MQTT broker is an extra process to manage in a "zero-configuration" app.

If you want pub/sub semantics without a broker, consider **NATS** (lightweight, embeddable, supports both pub/sub and request/reply).

**Recommendation:** Lean toward WebSockets for simplicity. Decide in Phase 1. Remove MQTT-specific language from the architecture doc or explicitly flag it as an alternative.

---

## 8. Network Discovery — mDNS

### What the docs say
"Use mDNS / Zero-Configuration Networking. The mobile app broadcasts a local signal searching for helperwatch.local. It instantly connects."

### Verdict: Unreliable on Android and WearOS. Needs a fallback.

mDNS works well on macOS and desktop Linux (Apple invented it as Bonjour). **On Android and WearOS, it is problematic:**

- WearOS devices frequently fail to resolve `.local` hostnames because they use hardcoded Google DNS servers.
- When a watch is Bluetooth-tethered to a phone (common on WearOS), multicast traffic doesn't propagate across the Bluetooth bridge.
- Android requires explicit `LOCAL_NETWORK` runtime permissions for mDNS/NSD discovery.
- WearOS aggressively drops Wi-Fi connections, making mDNS discovery intermittent.

**Recommendation:** Keep mDNS as a *first attempt* discovery method, but implement a robust fallback: the server hub should also advertise its IP via a simple UDP broadcast, or the pairing flow should allow manual IP entry / QR code scanning. The docs should not describe mDNS as seamless — it will be the most annoying part of setup for many users.

---

## 9. Remote Access — Tailscale / WireGuard

### What the docs say
Use Tailscale or WireGuard for secure remote access when the caregiver is away from home Wi-Fi.

### Verdict: Correct. Tailscale is the right recommendation.

Tailscale (which uses WireGuard under the hood) is the right tool. It creates encrypted peer-to-peer tunnels with zero port-forwarding configuration. It's free for personal use and well-maintained.

#### Gotcha

- This is a "documentation recommendation," not a bundled feature. Expecting a non-technical caregiver to install and configure Tailscale on both their phone and home computer is a real barrier. This should be framed as an advanced/optional feature, not a default expectation.
- For most families (child at home, parent in the same house or yard), this is unnecessary. The docs should lead with "you probably don't need this" rather than presenting it as a core feature.

**Recommendation:** Keep, but reframe as an advanced optional feature for specific use cases (e.g., teenager at school).

---

## 10. Caregiver Mobile App — React Native + Expo

### What the docs say
Build the caregiver app with React Native + Expo. "The gold standard."

### Verdict: Correct for the mobile app. Appropriate choice.

React Native + Expo is a well-established, mature path for cross-platform mobile apps. For a status dashboard, push-to-talk button, and routine trigger cards, it's a good fit. The claim is not overstated for the *mobile* app.

The only correction needed is removing the false claim about code sharing with the WearOS watch app (covered in section 2 above).

**Recommendation:** Keep for the mobile app. Remove WearOS code-sharing claims.

---

## Summary Table

| Technology | Verdict | Action Needed |
|------------|---------|---------------|
| Android/WearOS platform | **Correct** | Document battery/BLE throttling honestly |
| React Native on WearOS | **Wrong** | Correct to native Kotlin |
| ESP32 / BLE beacons | **Correct** | Strengthen multi-floor and re-calibration guidance |
| Electron / Tauri | **Correct** (prefer Tauri) | Fix "bundled Ollama" language |
| Whisper.cpp / Moonshine | **Correct** (prefer Moonshine) | Note English limitation |
| Ollama + small LLMs | **Correct with caveats** | Fix Pi 5 latency claims, clarify LLM role |
| MQTT / WebSockets | **Both viable** | Lean WebSockets, decide in Phase 1 |
| mDNS | **Unreliable on Android/WearOS** | Add fallback discovery mechanisms |
| Tailscale / WireGuard | **Correct** | Reframe as optional/advanced |
| React Native + Expo (mobile) | **Correct** | Remove WearOS code-sharing claim |

---

## Overall Assessment

Your skepticism is well-placed. The original discussion had the energy of a brainstorm, and it shows — technologies were named enthusiastically and presented with minimal friction, as if each one "just works." The *general direction* is sound (local-first, commodity hardware, open-source tools), and most of the named technologies are genuinely appropriate. But several claims are misleading:

1. **React Native on WearOS** is not a thing. The watch app must be native Kotlin.
2. **Background BLE scanning on WearOS** is far harder than described. This is the project's biggest technical risk.
3. **mDNS on Android/WearOS** is unreliable. A fallback discovery mechanism is mandatory.
4. **"Bundling" Ollama** is more complex than presented. It's a managed dependency, not an embedded library.
5. **Small model quality and Pi 5 latency** are overstated. The claims need honest correction.

None of these are project-killers. They're implementation realities that the docs should acknowledge rather than gloss over.
