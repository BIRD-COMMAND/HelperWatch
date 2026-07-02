# Getting Started

This guide walks caregivers through setting up HelperWatch in their home, from hardware setup to a running system.

## What You Need

### 1. A Smartwatch (~$35–100)

Any budget Android or WearOS smartwatch with:
- Wi-Fi
- Bluetooth
- Microphone and speaker
- Heart rate sensor
- Accelerometer

See [Hardware Guide](Hardware%20Guide.md) for specific recommendations.

### 2. Room Scanner Nodes (~$3–4 per room)

Small microcontroller boards (ESP32) placed in each room (typically 4–6 nodes for a standard home). They are plugged into standard USB wall outlets and scan for your child's watch.

See [Hardware Guide](Hardware%20Guide.md) for purchasing recommendations.

### 3. A HelperWatch Cloud Account

A secure account on the HelperWatch platform to manage routines, scripts, and connect devices.

### 4. A Smartphone

For the caregiver mobile app (Android or iOS).

### 5. Home Wi-Fi with Internet

Required for the smartwatch, smartphone, and ESP32 scanner nodes to connect to the Cloud Backend.

---

## Setup Overview

### Step 1: Create a HelperWatch Cloud Account

1. Download the HelperWatch caregiver app on your smartphone (Android or iOS) or visit the HelperWatch portal.
2. Sign up for a caregiver account. This registers your secure database in the cloud.

### Step 2: Flash and Set Up Room Scanner Nodes

1. Connect an ESP32 board to your computer or phone using a USB cable.
2. Open the browser-based **HelperWatch web flasher tool** (which uses WebUSB/WebSerial).
3. Enter your home Wi-Fi name/password and your HelperWatch account pairing token.
4. Click **"Flash Board."** The tool will upload the firmware and pair the node with your account automatically.
5. Unplug the board, place it in the room you wish to track, and plug it into a USB wall charger.
6. Repeat this process for each room node.

### Step 3: Register and Calibrate Rooms

1. Open the caregiver mobile app on your smartphone.
2. In settings, you will see a list of paired scanner nodes. Assign a friendly room name to each node (e.g., "Kitchen", "Bathroom", "John's Bedroom").
3. Walk to each room with the child's smartwatch, open the calibration screen, and tap **"Calibrate [Room Name]"** while standing in that room. The system will record baseline BLE signals.

### Step 4: Pair the Smartwatch

1. Install the HelperWatch watch app on the child's smartwatch.
2. Open the watch app. A 6-digit pairing code will be displayed.
3. In the caregiver mobile app on your phone, tap **"Add Wearable"** and enter the pairing code. The watch will now begin advertising its BLE ID and connect to your account over WSS.

### Step 5: Configure Routines

1. Set up daily routines (morning, bedtime, mealtimes, etc.) in the caregiver mobile app.
2. Customize the AI's tone, voice prompting style, and scripting rules.
3. Review and approve the prompt templates that the AI classifier will select from.

### Step 6: Start Monitoring

1. Place the smartwatch on your child.
2. Ensure they are wearing their Bluetooth earbuds (if default privacy mode is selected).
3. Monitor room transitions, heart rate metrics, and current task steps in real-time on your mobile app dashboard.

---

## Important: Read Before Using

Before deploying HelperWatch, please read:

- [Safety and Ethics Overview](../ethics/Safety%20and%20Ethics%20Overview.md) — Understand the system's capabilities and ethical guardrails.
- [Privacy and Data Sovereignty](../ethics/Privacy%20and%20Data%20Sovereignty.md) — How your family's data is handled.
- [Risks and Mitigations](../ethics/Risks%20and%20Mitigations.md) — Known risks and how they are addressed.

## Need Help?

See the [FAQ](FAQ.md) for common questions and troubleshooting.

## Related Documents

- [Hardware Guide](Hardware%20Guide.md) — Detailed hardware recommendations and purchasing links
- [System Architecture](../design/System%20Architecture.md) — Technical component overview
- [Cloud Backend](../design/Cloud%20Backend.md) — How data is managed in the cloud

