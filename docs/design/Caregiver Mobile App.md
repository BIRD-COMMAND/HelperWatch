# Caregiver Mobile App

## Overview

The caregiver mobile app is the primary interface through which parents interact with the HelperWatch system. Built with React Native + Expo for cross-platform deployment (Android and iOS), it provides real-time visibility into the child's status, direct control over AI behavior, and historical trend data — all without requiring the caregiver to walk to a desktop computer.

## Design Philosophy: Calm UI

Parents of severely impacted children are already in a state of chronic sensory and emotional overload. The app must actively **reduce** cognitive load, not add to it. It follows the principles of Calm Technology (Weiser & Brown, 1997) — silently monitoring in the background and only demanding attention when truly necessary.

The app should not look like a frantic fitness tracker or a data-heavy dashboard. It should feel like a quiet reassurance that things are under control.

## Key Screens and Features

### Status-at-a-Glance Home Screen

When a parent opens the app, they should know within one second whether things are okay.

**Visual Elements:**

- **Avatar / Status Ring** — A central, calming visual element for each child:
  - Green ring: routine in progress, heart rate stable.
  - Pulsing yellow ring: stuck on a task step for more than a configurable duration.
  - Red ring: biometric spike or possible distress.
- **Contextual Presence** — Current room and active task displayed clearly: *"Leo is in the Bedroom (Brushing Teeth)."*
- **Live Subtitle Feed** — A small, real-time text stream showing what the AI last said or is currently doing: *"AI: 'Great job, now grab your shoes.'"* This lets the parent monitor contextually without physically interrupting the child.

### Push-to-Talk Intercom Mode

The most powerful tool for real-time caregiver intervention.

**How it works:**

1. The parent holds a prominent button and speaks a command: *"Tell Leo it's almost dinner time and he needs to pick up his blocks."*
2. The app sends this text injection to the local server.
3. The AI integrates the parent's command into its next verbal cue on the watch: *"Hey Leo, your dad says it's almost dinner time! Let's put away the blocks first."*

The cue still comes from the familiar, patient AI voice — preventing the jarring shift of a direct walkie-talkie blast — while giving the parent absolute control. This preserves the child's focus and routine flow.

### Macro Routine Triggers

A grid of large, customizable one-tap cards for common friction points:

- **Start Bedtime Routine**
- **Transition: Leaving the House**
- **Cool Down / Meltdown Intercept**
- **Free Time / Pause Monitoring**

Tapping a card shifts the entire AI orchestration profile — altering prompt frequency, biometric alert thresholds, and the active script set.

### Trend Reports and Data Visualization

<!-- TODO: Specific chart types and data views will be designed during Phase 4. -->

The app surfaces locally-stored historical data to help caregivers and clinicians identify patterns:

- Room transition timelines.
- Task completion durations over days and weeks.
- Heart rate patterns correlated with rooms, times, and activities.
- Meltdown frequency and pre-meltdown biometric signatures.

All data is read from the local server. Nothing is transmitted externally.

### Alert and Notification System

The app delivers push notifications for critical events:

- Biometric meltdown intercept triggered.
- Child unresponsive to prompts for an extended period.
- Watch battery low or connectivity lost.

Non-critical status updates (routine completed, task step advanced) are available in-app but do not generate push notifications by default, following the Calm UI principle.

## Technical Stack

**React Native + Expo** — Write the UI once in TypeScript, compile to native Android and iOS apps. Expo provides a managed build pipeline that simplifies distribution.

**Shared code opportunity:** Because the WearOS watch app can also be built with React Native, significant data-handling, state-management, and networking code can be shared between the watch and mobile apps.

## Network Communication

### Local (Home Wi-Fi)

The app discovers the local server hub via **mDNS** (`helperwatch.local`), connecting over WebSockets. No manual IP configuration. Data transfer is instantaneous, uses no cellular data, and never leaves the house.

### Remote (Away from Home)

When the caregiver is outside Wi-Fi range (e.g., backyard, driveway, or away from home while another caregiver is present):

- **Tailscale or WireGuard** creates an encrypted peer-to-peer tunnel between the phone and the home server.
- To the app, it appears as if the phone is still on the home network.
- No middleman corporation logs the data.

Setup instructions for remote access are provided in the [Getting Started](../guides/Getting%20Started.md) guide.

## References

- Weiser, M., & Brown, J. S. (1997). The coming age of calm technology. In *Beyond calculation* (pp. 75-85). Springer, New York, NY.

## Related Documents

- [System Architecture](System%20Architecture.md) — How the mobile app fits into the system
- [Local Server Hub](Local%20Server%20Hub.md) — The backend the app communicates with
- [Getting Started](../guides/Getting%20Started.md) — Caregiver setup walkthrough
