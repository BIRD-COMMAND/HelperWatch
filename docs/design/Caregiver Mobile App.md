# Caregiver Mobile App

## Overview

The caregiver mobile app is the primary interface through which parents interact with the HelperWatch system. Built with React Native + Expo for cross-platform deployment (Android and iOS), it provides real-time visibility into the child's status, direct control over AI behavior, and historical trend data — connecting directly to the secure Cloud Backend.

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
2. The app sends this text injection to the Cloud Backend.
3. The AI integrates the parent's command into its next verbal cue on the watch: *"Hey Leo, your dad says it's almost dinner time! Let's put away the blocks first."*

The cue still comes from the familiar, patient AI voice — preventing the jarring shift of a direct walkie-talkie blast — while giving the parent absolute control. This preserves the child's focus and routine flow.

### Macro Routine Triggers

A grid of large, customizable one-tap cards for common friction points:

- **Start Bedtime Routine**
- **Transition: Leaving the House**
- **Cool Down / Meltdown Intercept**
- **Free Time / Pause Monitoring**

Tapping a card shifts the entire AI orchestration profile — altering prompt frequency, biometric alert thresholds, the active script set, and protocol parameters. Macros are the caregiver's emergency buttons — one tap should change everything that needs to change, instantly.

See also: [Expanded Macro Triggers](#expanded-macro-triggers) for protocol-aware macro configuration.

### Trend Reports and Data Visualization

The app surfaces encrypted historical data from the Cloud Backend to help caregivers and clinicians identify patterns:

- Room transition timelines.
- Task completion durations over days and weeks.
- Heart rate patterns correlated with rooms, times, and activities.
- Meltdown frequency and pre-meltdown biometric signatures.
- Day quality score history over weeks — are things trending better or worse?
- Step-level aversion patterns — which routine steps are consistently abandoned?
- Perseveration frequency and topic tracking.

If historical logging is disabled by the caregiver, this screen will be inactive and no trend data is kept in the cloud.

### Alert and Notification System

The app delivers push notifications for critical events:

- Biometric meltdown intercept triggered.
- Child unresponsive to prompts for an extended period.
- Watch battery low or connectivity lost.

Non-critical status updates (routine completed, task step advanced) are available in-app but do not generate push notifications by default, following the Calm UI principle.

See also: [Expanded Alerts](#expanded-alerts) for protocol-driven notifications.

---

## Child Profile Management

The child profile is the system's operating model for an individual child — how they communicate, what they can handle, what triggers them, and what helps. The full profile specification is defined in the [Adaptive Response Architecture](Adaptive%20Response%20Architecture.md). This section describes how the caregiver interacts with that profile through the app.

### The UX Challenge

The child profile has 30+ configurable fields across 6 sections. Presenting all of them at once would overwhelm a caregiver who is already in chronic overload — violating the Calm UI principle before the system even starts working. But the system's effectiveness is directly proportional to profile completeness. The app must reconcile these tensions.

### Solution: Progressive Disclosure

The app reveals profile complexity gradually, gated by the caregiver's readiness:

**Tier 1 — Day One (required, ~2 minutes)**

The minimum viable profile. The caregiver enters:
- Child's name
- Primary communication mode (verbal / limited verbal / non-verbal)
- Working memory capacity (1-step / 2-step / multi-step instructions)
- One basic routine (selected from a template or built from scratch)

This is enough to run the core prompting loop with conservative defaults.

**Tier 2 — First Week (prompted, ~10 minutes total)**

After the system has been running for a few days, the app gently surfaces expansion prompts — not modal interruptions, but in-context suggestions within the profile screen:

- *"Would you like to set [Child]'s transition tolerance? This helps the system choose the right countdown timing."*
- *"Does [Child] have known meltdown precursors? Adding them helps the system catch meltdowns earlier."*
- *"How does [Child] respond to direct praise? Some children prefer indirect affirmation."*

Each prompt links to a single field or small field group. The caregiver can skip any prompt and return later.

**Tier 3 — First Month (available, never forced)**

The full profile is always accessible from the profile screen, organized into its 6 sections. But fields the caregiver hasn't filled are shown as "using default" rather than blank — communicating that the system is working, just not optimally.

A progress indicator (not a progress *bar* — something less anxiety-inducing, perhaps a quiet "Profile depth: Foundational / Detailed / Comprehensive" label) shows how complete the profile is without creating a sense of homework.

### Template Profiles

On first setup, the caregiver can choose to start from a template:

| Template | Description |
|----------|-------------|
| **Non-verbal, severe executive dysfunction** | Haptic-first cues, extended response latency, maximum scaffolding, conservative fading |
| **Verbal, impulsive, attention-seeking** | Proactive engagement at short intervals, redirect-heavy protocols, firm + warm boundaries |
| **Verbal, anxious, perseverative** | Extended answer phase, gentle tone, slow transitions, calming protocols prioritized |
| **Limited verbal, sensory-sensitive** | Low-volume or haptic-only cues, extended processing time, sensitive shutdown thresholds |
| **Start from scratch** | All defaults, no template — for caregivers who prefer to configure everything manually |

Templates populate all profile fields with reasonable defaults. The caregiver can override any field at any time. The template is a starting point, not a commitment.

### Profile Editing

Individual fields are edited in-place with simple, non-technical language:

- Dropdowns with plain-language options (not clinical terminology)
- Slider controls for thresholds (e.g., perseveration phase boundaries)
- Free-text fields for custom phrase mappings (e.g., "when [Child] says 'all done', they mean they want to stop")
- Toggle switches for binary options (e.g., "uses humor: yes/no")

Every field includes a brief explanation of what it does and why it matters, accessible via an info icon — never cluttering the primary view.

---

## Routine and Protocol Configuration

Routines and protocols are the system's instructions for *what to do*. The [Adaptive Response Architecture](Adaptive%20Response%20Architecture.md) defines the protocol structure; this section describes how the caregiver builds and customizes them through the app.

### Routine Builder

A routine is an ordered list of steps with configurable parameters:

**Step definition:**
- Step name (*"Pick up toothbrush"*)
- Expected room (auto-advances step context when child enters this room)
- Expected duration (triggers stuck detection if exceeded)
- Prompt text (the verbal cue for this step)
- Completion signal (time-based, verbal confirmation keyword, or room transition)
- Micro-affirmation text (what the system says when the step is done)

**Routine-level settings:**
- Schedule (recurring times, or manual trigger only)
- Dynamic fading (on/off, aggressiveness)
- Prompt hierarchy direction — presented as "How should the system prompt [Child] for this routine?" with two options:
  - *"Start gentle, get more specific if needed"* (Least-to-Most — best for established routines)
  - *"Start with full instructions, fade over time"* (Most-to-Least — best for new routines)
- Reinforcement schedule — presented as "How often should the system encourage [Child] during this routine?" with options:
  - *"After every step"* (Continuous — default for new routines)
  - *"After some steps, varied"* (Variable Ratio — best for established routines)
  - *"Only at the end"* (Chain-End — for children who find per-step praise disruptive)
  - *"No verbal encouragement"* (Minimal — for children uncomfortable with praise)
- Associated macro trigger (link to a one-tap card)

**Template routines:**
Pre-built routines for common sequences:
- Morning routine (wake up → toilet → brush teeth → get dressed → breakfast → shoes and backpack)
- Bedtime routine (pajamas → brush teeth → book → lights out)
- Leaving the house (shoes → jacket → backpack → door)
- Mealtime (wash hands → sit → eat → clear plate)

Caregivers select a template, rename steps to match their household language, adjust expected durations, and customize prompt text. Or they build from scratch.

### Protocol Parameter Adjustment

Most caregivers should never need to interact with protocol internals. The template profiles and template protocol sets provide working defaults. But for caregivers who want fine-grained control — or when the system suggests a parameter change based on observed patterns — the app provides access to key configurable values:

**Perseveration settings:**
- Phase thresholds (N₁, N₂, N₃) — presented as "after how many repetitions should the system start redirecting? Start holding boundaries?"
- Repetition time window — "how long before the repetition counter resets?"
- Per-question answer registry — "what should the system say when [Child] asks [specific question]?"

**Transition settings:**
- Countdown intervals and warning count
- Bridging cue templates
- De-escalation timeout

**Attention/engagement settings:**
- Proactive check-in interval — "how often should the system reach out?"
- Engagement duration — "how many follow-up exchanges?"

**Meltdown detection settings:**
- Heart rate trend sensitivity
- Build-up notification lead time
- Peak response mode (haptic-only / silence / caregiver-defined)
- Recovery buffer duration

**Bedtime settings:**
- Graduated firmness rate
- Request allowance rules (water: once, bathroom: always, questions: defer)
- Companion mode (on/off, ambient sound selection)

**Prompting and reinforcement settings:**
- Default prompt hierarchy direction for new routines — "When [Child] starts a brand-new routine, should the system start with full instructions or start gentle?"
- Prompt dependency detection — on/off. When on, the system alerts the caregiver if [Child] consistently waits for the most detailed prompt before acting.
- Reinforcement variety — "Should the system vary its encouragement phrases?" (Recommended: on. Prevents "Good job!" fatigue.)
- Reinforcement fading suggestions — on/off. When on, the system suggests reducing encouragement frequency as [Child] masters a routine.

Each setting is presented in plain language with the current value and a brief explanation of what changing it does. Advanced settings are hidden behind an "Advanced" toggle — available but not visible by default.

### System-Suggested Configuration Changes

When the system detects a pattern that suggests a configuration change would help, it surfaces a non-intrusive suggestion card in the app:

- *"[Child] has abandoned the tooth-brushing step 8 out of the last 10 days. Would you like to break this step into smaller sub-steps?"*
- *"Redirecting during perseveration at phase 2 seems to increase repetition count. Would you like to extend the answer phase?"*
- *"[Child]'s meltdowns this week have all been preceded by 15+ minutes of elevated heart rate. Would you like to lower the early warning threshold?"*

Suggestions are informational. The caregiver can accept, dismiss, or defer them. The system never changes its own configuration without caregiver approval.

---

## Day View / Context Dashboard

A new screen providing real-time visibility into what the system is "thinking." This is designed for caregivers who want to understand the system's decision-making without needing to read protocol tables.

### Day Quality Indicator

A simple, glanceable visual showing the child's day quality score:

- **Visual metaphor:** A calm, abstract indicator (not a number) that conveys "good day," "mixed day," or "tough day." This could be a color gradient on the home screen status ring — shifting from warm green through amber to cool blue — or a simple text label.
- **Tapping the indicator** expands a timeline view showing the events that contributed to the current score: routine completions (+), successful transitions (+), meltdowns (−), conflicts (−), escalations (−).

The day quality score is a *caregiver transparency tool*, not a child report card. It helps the caregiver understand why the system is being gentler or firmer than usual and calibrate their own expectations for the rest of the day.

### Active Protocol View

When a protocol is currently active (e.g., meltdown build-up detected, or perseveration response in progress), the app shows:

- **What triggered it** — *"Repeated question detected: 'What's for dinner?' (12th time in 35 minutes)"*
- **What phase the system is in** — *"Redirect phase — gently pivoting to current activity"*
- **What happens next** — *"If repetition continues, the system will shift to acknowledge-and-hold at repetition 15"*
- **Caregiver override option** — A clear button to dismiss the protocol, change the response, or escalate to manual handling.

This view is not a required monitoring screen. It is available when the caregiver wants to understand what the system is doing and why. Following Calm UI, the app does not push this information — it makes it accessible.

### Recent Events Timeline

A chronological feed of significant events from the current day:

- *9:15 AM — Morning routine completed (18 minutes)*
- *10:42 AM — Proactive check-in: responded positively*
- *11:30 AM — Perseveration: "What's for lunch?" (answered 8 times, redirected at 6)*
- *1:15 PM — Transition to lunch: successful (countdown + bridging)*
- *2:40 PM — Meltdown build-up detected → early intervention → resolved without peak*

Each event is tappable for detail. The feed is scrollable but not overwhelming — events are bucketed by severity, and only notable events appear (routine step completions are summarized, not listed individually).

---

## Setup Wizard / Guided Onboarding

The first-run experience for a new HelperWatch family. This implements the consultancy-informed setup process described in the [Adaptive Response Architecture](Adaptive%20Response%20Architecture.md), translated into an in-app guided experience.

### Wizard Flow

**Step 1 — Account and Household**
- Create caregiver account (email, password)
- Name the household
- Add child (name, age — age is optional and used only for UX adaptation, not protocol logic)

**Step 2 — Quick Profile**
- Template selection (or "start from scratch")
- 3–5 essential questions based on the selected template:
  - "How does [Child] communicate?" (verbal / limited verbal / non-verbal)
  - "How many steps can [Child] follow at once?" (1 / 2 / 3+)
  - "How does [Child] handle transitions?" (easy / moderate / difficult)
- These map directly to the Tier 1 profile fields.

**Step 3 — First Routine**
- Select a template routine (morning, bedtime, mealtime) or create custom
- Customize step names and prompt text
- Set the routine schedule

**Step 4 — Device Pairing**
- Pair the smartwatch (6-digit code)
- Flash and register ESP32 room scanner nodes (web-based tool)
- Room calibration walkthrough

**Step 5 — Done**
- Summary of what was set up
- Invitation to explore the profile ("You can add more detail to [Child]'s profile anytime — the more the system knows, the better it can help")
- Link to the Getting Started guide for reference

The wizard is designed to take **under 15 minutes** for steps 1–3 (profile and first routine), with device pairing (step 4) taking additional time depending on hardware readiness.

### Facilitator Mode

For families working with a consultant, clinician, or experienced volunteer, the wizard includes a **facilitator mode** toggle. In facilitator mode:
- All profile fields are accessible from the start (no progressive disclosure gates)
- Protocol parameters are exposed alongside profile fields
- The facilitator can configure the system to the depth required in a single session
- A "handoff summary" is generated at the end, showing the caregiver what was configured and how to adjust it later

---

## Expanded Macro Triggers

Building on the basic macro grid, macros are expanded to support the protocol-aware system:

### Protocol Profile Shifts

Each macro card now maps to a full protocol profile shift, not just a script change:

| Macro | Profile Shift |
|-------|--------------|
| **Bedtime** | Softer tone, lower biometric alert sensitivity, bedtime protocol set active, companion mode available, room-exit tracking enabled |
| **Leaving the House** | Transition protocol active, countdown sequence started, first-step anchoring, time awareness cues enabled |
| **Cool Down** | Calming protocols prioritized, prompt intensity reduced, haptic-only option, caregiver check-in prompted |
| **Free Time** | Proactive engagement active at configured interval, routine scaffolding paused, relaxed biometric thresholds |
| **Focus Time** | Proactive engagement paused, reduced interruptions, only critical alerts |

### Custom Macros

Caregivers can create custom macros by defining:
- A name and icon for the card
- Which protocol parameters to override
- What prompt tone/style to use
- Whether to notify the caregiver when the macro auto-deactivates

Example custom macros:
- *"Grandma's House"* — Adjusted sensitivity (unfamiliar environment), increased caregiver alerts, simplified routines
- *"Doctor's Appointment"* — Reduced audio capture (privacy), calming tone, transition support active
- *"Sibling Playdate"* — Conflict detection sensitivity increased, proactive engagement at higher frequency

---

## Expanded Alerts

The alert system is expanded to support protocol-driven notifications with context:

### Alert Categories

| Category | Push Notification | Context Provided |
|----------|------------------|-----------------|
| **Meltdown build-up** | Yes (early warning) | Heart rate trend, movement pattern, time since onset, what the system is trying |
| **Meltdown peak** | Yes (urgent) | Biometric readings, room, duration, system is in prompt suppression mode |
| **Sensory shutdown** | Yes | Duration of non-responsiveness, room, system has suppressed prompts |
| **Perseveration boundary** | Optional | Question, repetition count, current phase, what the system is doing |
| **Sibling conflict** | Yes | Room, biometric spike, transcript indicators |
| **Task abandonment** | Optional | Which routine step, how long since departure, room the child went to |
| **Routine completed** | In-app only | Duration, steps completed independently vs. prompted |
| **Transition failed** | Optional | Which transition, child's response, what the system tried |
| **Watch disconnected** | Yes | Last known room, last known biometrics, time since disconnect |
| **System suggestion** | In-app only | Suggested configuration change with rationale |

### Alert Detail View

Tapping an alert opens a detail view with:
- **What happened** — Plain-language description of the event
- **What the system did** — Which protocol activated, what responses were delivered
- **What you can do** — Actionable options (dismiss, override protocol, trigger a macro, navigate to the child)
- **Related trend** — If this type of event has been recurring, a brief trend note: *"This is the 3rd meltdown build-up this week — all preceded by transition warnings."*

### Alert Fatigue Prevention

Following Calm UI, the alert system is designed to prevent notification overload:

- **Smart batching:** Multiple related events within a short window are batched into a single notification (*"3 events in the last 10 minutes — tap for details"*)
- **Caregiver-configurable priority:** The caregiver chooses which categories generate push notifications vs. in-app-only entries
- **Quiet hours:** Notifications during configured quiet periods (e.g., after the caregiver's own bedtime) are silenced unless the alert is at the "urgent" tier (meltdown peak, watch disconnect)
- **Context-aware suppression:** If the caregiver is actively viewing the app, push notifications are suppressed in favor of in-app indicators

---

## Technical Stack

**React Native + Expo** — Write the UI once in TypeScript, compile to native Android and iOS apps. Expo provides a managed build pipeline that simplifies distribution.

> **Note:** The WearOS watch app is built natively in Kotlin (React Native does not support WearOS). The watch app and mobile app are entirely separate codebases that communicate through the shared Cloud Backend API.

## Network Communication

### Cloud Connection

The mobile app connects directly to the Cloud Backend over secure WebSocket (WSS) and HTTPS protocols. 

Because the backend is hosted at a public domain, **remote access is supported natively**. Caregivers can monitor their child from anywhere—whether they are in the backyard, at work, or if the child is out with a respite caregiver—without requiring complex local networks, home routing setups, or peer-to-peer VPN configurations.

Device provisioning is done by logging into the caregiver account and entering a pairing code displayed on the smartwatch or flashing ESP32 boards using a browser tool.

## References

- Weiser, M., & Brown, J. S. (1997). The coming age of calm technology. In *Beyond calculation* (pp. 75-85). Springer, New York, NY.

## Related Documents

- [System Architecture](System%20Architecture.md) — How the mobile app fits into the system
- [Cloud Backend](Cloud%20Backend.md) — The cloud services handling API requests and routine configurations
- [Adaptive Response Architecture](Adaptive%20Response%20Architecture.md) — Child profiles and protocol configuration managed through this app
- [Behavioral Use Cases](Behavioral%20Use%20Cases.md) — Behavioral scenarios the app's configuration supports
- [Getting Started](../guides/Getting%20Started.md) — Caregiver setup walkthrough
