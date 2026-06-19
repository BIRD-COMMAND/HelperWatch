# Getting Started

This guide walks caregivers through setting up HelperWatch in their home, from hardware acquisition to a working system.

<!-- TODO: This guide will be fully detailed once the software reaches a usable prototype (Phase 3-4). The current content describes the intended setup experience and hardware requirements. -->

## What You Need

### 1. A Smartwatch (~$35–80)

Any budget Android or WearOS smartwatch with:

- Wi-Fi
- Bluetooth
- Microphone and speaker
- Heart rate sensor
- Accelerometer

See [Hardware Guide](Hardware%20Guide.md) for specific recommendations.

### 2. Room Beacons (~$3–15 per room)

Small devices placed in each room to track your child's location. You need one per room (typically 4–6 for a standard home).

**Choose one:**
- **ESP32 boards** (~$3–4 each) — Plug into USB wall chargers. Requires a one-time firmware flash.
- **Commercial BLE beacons** (~$10–15 each) — Stick to the wall. No setup required.

See [Hardware Guide](Hardware%20Guide.md) for purchasing links and setup instructions.

### 3. A Home Computer

HelperWatch runs on your existing home computer. No special hardware is needed.

- **Windows** PC or laptop (Intel i5 or better recommended)
- **Mac** (any Apple Silicon Mac is ideal)
- **Linux** PC (community-supported)
- Or a **Raspberry Pi 5** as a dedicated hub (~$35)

See [Hardware Guide](Hardware%20Guide.md) for performance expectations by hardware tier.

### 4. A Smartphone

For the caregiver mobile app (Android or iOS).

### 5. Home Wi-Fi

A standard home Wi-Fi network. No internet connection is required for core functionality (the system runs entirely on your local network).

## Setup Overview

### Step 1: Install the Server Hub

<!-- TODO: Download links and platform-specific instructions will be added at release. -->

1. Download the HelperWatch installer for your computer (Windows `.exe` or Mac `.dmg`).
2. Run the installer. The application handles all dependencies automatically.
3. Launch HelperWatch. The app will benchmark your computer and download the appropriate AI model.

### Step 2: Set Up Room Beacons

1. Place one beacon in each room you want to track.
2. Power them on (USB for ESP32, or activate commercial beacons).
3. In the HelperWatch server app, click **"Scan for Beacons."**
4. Assign a room name to each detected beacon.

### Step 3: Calibrate Room Positioning

1. Take the smartwatch to each room.
2. In the app, tap **"I'm in [Room Name]"** while standing in that room.
3. Repeat for each room. This creates a signal map for accurate room detection.

### Step 4: Pair the Smartwatch

1. Install the HelperWatch watch app on the smartwatch.
2. Connect the watch to your home Wi-Fi network.
3. In the server hub app, click **"Scan for Watch."** A 6-digit pairing code will appear.
4. Enter the pairing code on the watch.

### Step 5: Install the Mobile App

1. Download the HelperWatch caregiver app on your phone (Android or iOS).
2. Open the app while connected to your home Wi-Fi.
3. The app will automatically discover the server hub on your network.

### Step 6: Configure Routines

1. Set up your child's daily routines (morning, bedtime, mealtimes, etc.) using the server hub or mobile app.
2. Customize the AI's voice, tone, and prompting style.
3. Review and approve the script set that the AI will use for verbal cues.

### Step 7: Start Using HelperWatch

Place the watch on your child and monitor from the mobile app. Start with supervised sessions to calibrate your comfort level and the system's accuracy.

## Important: Read Before Using

Before deploying HelperWatch, please read:

- [Safety and Ethics Overview](../ethics/Safety%20and%20Ethics%20Overview.md) — Understand the system's capabilities and ethical guardrails.
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — How your family's data is handled.
- [Risks and Mitigations](../ethics/Risks%20and%20Mitigations.md) — Known risks and how they are addressed.

## Need Help?

See the [FAQ](FAQ.md) for common questions and troubleshooting.

## Related Documents

- [Hardware Guide](Hardware%20Guide.md) — Detailed hardware recommendations and purchasing
- [System Architecture](../design/System%20Architecture.md) — How the system works technically
