# Implementation Plan: HelperWatch Architecture Pivot Documentation Update

Fully update all project documents to reflect the simplified architecture from the approved Architecture Pivot Analysis. This includes transitioning the project from local-first to cloud-first, replacing watch-side BLE scanning with ESP32-side scanning, and removing eliminated components from the documentation.

## Proposed Changes

### Deletions
* [DELETE] [Local Server Hub.md](file:///c:/dev/HelperWatch/docs/design/Local%20Server%20Hub.md)
* [DELETE] [Initial Project Discussion Log.md](file:///c:/dev/HelperWatch/docs/Initial%20Project%20Discussion%20Log.md)

### New Documents
* [NEW] [Cloud Backend.md](file:///c:/dev/HelperWatch/docs/design/Cloud%20Backend.md): Outlines the architecture of the new cloud backend, including STT (Moonshine default), LLM orchestration (Groq/OpenAI, classifier/router model), routine management, telemetry APIs, and data retention/privacy design.

### Modifications
* [MODIFY] [Project Brief.md](file:///c:/dev/HelperWatch/docs/Project%20Brief.md): Update the technical philosophy and ethical guardrails to reflect the cloud-first model (on-watch STT, encrypted transit, transient cloud memory, zero persistence) and the ESP32 room scanner setup.
* [MODIFY] [System Architecture.md](file:///c:/dev/HelperWatch/docs/design/System%20Architecture.md): Update component diagram, remove local server references, document cloud endpoints, remove mDNS/Tailscale, and specify new ESP32-scanning data flows.
* [MODIFY] [Wearable App.md](file:///c:/dev/HelperWatch/docs/design/Wearable%20App.md): Change BLE scanning to BLE advertising. Detail on-device transcription (Moonshine STT) as the privacy default. Remove local server telemetry destinations, replacing them with secure cloud WebSocket/HTTPS endpoints. Update battery expectations (full-day target due to passive BLE advertising).
* [MODIFY] [Caregiver Mobile App.md](file:///c:/dev/HelperWatch/docs/design/Caregiver%20Mobile%20App.md): Pivot connection details to direct cloud secure socket connection. Remove local network discovery (mDNS, UDP broadcast, QR pairing) and Tailscale/WireGuard remote tunnels. Update data visualization and dashboard sources to point to the Cloud Backend.
* [MODIFY] [Indoor Positioning.md](file:///c:/dev/HelperWatch/docs/design/Indoor%20Positioning.md): Invert the BLE scanning direction (watch broadcasts advertisements, ESP32 room nodes scan and report RSSI values via Wi-Fi to the Cloud Backend). Delete commercial BLE beacon tag option. Update deployment guide and accuracy considerations (lowering transition latency).
* [MODIFY] [Hardware Guide.md](file:///c:/dev/HelperWatch/docs/guides/Hardware%20Guide.md): Remove local PC/Mac/Pi server hardware tiers. Add Cloud Backend hosting estimates. Remove commercial BLE tags. Focus on ESP32 scanner nodes and WearOS smartwatches (noting improved battery life from BLE advertising).
* [MODIFY] [Getting Started.md](file:///c:/dev/HelperWatch/docs/guides/Getting%20Started.md): Update setup sequence to: Cloud account creation -> ESP32 node flashing/placement -> room registration in caregiver app -> smartwatch pairing/calibration.
* [MODIFY] [FAQ.md](file:///c:/dev/HelperWatch/docs/guides/FAQ.md): Update privacy, hardware requirements, and networking questions to reflect the cloud-first/ESP32-scanning structure.
* [MODIFY] [Project Roadmap.md](file:///c:/dev/HelperWatch/docs/project/Project%20Roadmap.md): Realign roadmap phases to develop ESP32 scanner firmware, Cloud Backend API, and watch BLE advertising, removing local server/auto-benchmarking milestones.
* [MODIFY] [Mission and Values.md](file:///c:/dev/HelperWatch/docs/project/Mission%20and%20Values.md): Align the "Ethical Safety by Design" and "Radical Accessibility" sections with the new cloud backend and on-watch STT features.
* [MODIFY] [Contributing.md](file:///c:/dev/HelperWatch/docs/project/Contributing.md): Update contribution guidelines to reflect the new privacy-by-design cloud constraints.
* [MODIFY] [Glossary.md](file:///c:/dev/HelperWatch/docs/reference/Glossary.md): Update terminology (BLE, ESP32, RSSI, room scanner nodes, data sovereignty, Moonshine, WebSockets) and remove obsolete terms (mDNS, Ollama, local server hub, Tailscale).
* [MODIFY] [Privacy and Data Sovereignty.md](file:///c:/dev/HelperWatch/docs/ethics/Privacy%20and%20Data%20Sovereignty.md): Reframe privacy guarantees around on-watch STT, encrypted transit, transient cloud memory, and zero persistence. Remove local server hub and cloud API fallback toggle.
* [MODIFY] [Risks and Mitigations.md](file:///c:/dev/HelperWatch/docs/ethics/Risks%20and%20Mitigations.md): Update existing risk entries to match the cloud model. Add new entries for internet/cloud dependency and ESP32 flashing/setup complexity.
* [MODIFY] [Safety and Ethics Overview.md](file:///c:/dev/HelperWatch/docs/ethics/Safety%20and%20Ethics%20Overview.md): Update requirements to specify data sovereignty via on-device transcription and transient cloud processing.

## Verification Plan

### Automated Tests
- Run a link checker or search for broken internal markdown links across the documentation files.

### Manual Verification
- Verify that every file in the directory structure matches the new architecture.
- Verify that all references to the "local server hub", "Ollama", "mDNS", and "Tailscale" have been removed or updated.
- Verify that the markdown links resolve correctly and use forward slashes.
- Verify that `Initial Project Discussion Log.md` and `Local Server Hub.md` are deleted.
