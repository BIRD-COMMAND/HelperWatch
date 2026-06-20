# Adaptive Response Architecture

## Introduction

The [Behavioral Use Cases](Behavioral%20Use%20Cases.md) document describes ten real-world scenarios HelperWatch must handle. This document specifies *how the system decides what to do* in any given moment — the architecture that connects a child's profile, their current state, the kind of day they are having, and the specific behavioral signal the system just observed to produce the right response.

"Right" is doing a lot of work in that sentence. For the children this system serves, there is rarely a perfect answer. But there is usually a *pretty good* answer, and the distance between "pretty good" and "no answer at all" (or "the wrong answer") is the distance between a manageable day and an unmanageable one. The system's job is to consistently land in "pretty good" territory across hundreds of micro-decisions per day, for a child whose needs are specific, whose patterns are learnable, and whose caregivers know exactly what works — they just cannot physically execute it 14 hours a day without breaking.

This document covers three tightly coupled systems:

1. **Child Profile** — A structured, functional description of who this child is: how they communicate, what they can handle, what sets them off, and what helps them recover.
2. **Response Protocol Engine** — The decision system that evaluates incoming signals against the child's profile and current context to select the appropriate response from a library of caregiver-defined protocols.
3. **Configuration and Setup** — The practical process for building a child's profile and protocol set, from first-day defaults to long-term refinement.

These three systems are the brain of HelperWatch. The watch, the room scanners, and the cloud backend are the nervous system. Without this architecture, the nervous system has nothing to think with.

---

## Child Profile Specification

The child profile is not a medical record, a diagnostic summary, or an IEP. It is a **functional operating model** — a structured description of how this specific child interacts with the world, encoded in a form the response protocol engine can read.

Every field in the profile exists because at least one protocol decision branch depends on it. There are no decorative fields.

### Communication Profile

How the child uses and processes language. This is the single most important profile section because it determines whether verbal cues are even useful, what form they should take, and how the system interprets incoming speech.

| Field | Purpose | Example Values |
|-------|---------|---------------|
| **Primary communication mode** | Determines whether the system defaults to verbal, haptic, or visual cues | Verbal, Limited verbal, Non-verbal, AAC-assisted |
| **Receptive language level** | How complex an instruction the child can process | Single-step concrete ("pick up the toothbrush"), Two-step ("pick up the toothbrush and put paste on it"), Multi-step |
| **Auditory processing latency** | How long the system should wait for a response before re-prompting | Short (3–5s), Moderate (5–10s), Extended (10–20s), Highly variable |
| **Echolalia pattern** | Whether and how the child echoes, so the system can classify echoed speech correctly | None, Immediate echo (repeats what was just said), Delayed echo (scripting from memory), Mixed |
| **Scripting tendency** | Whether the child recites memorized dialogue and whether this is self-regulatory or communicative | None, Self-regulatory (leave alone), Communicative (interpret), Mixed |
| **Verbal expression capacity** | Whether the child can articulate needs, answer questions, or report on their state | Full sentences, Keyword/phrase level, Single words, Non-verbal with gestures, Non-verbal without gestures |
| **Known communicative phrases** | Caregiver-defined mappings of the child's specific phrases to their actual meaning | "All done" = wants to stop current activity; "Go car" = wants to leave the house |

### Sensory Profile

How the child experiences sensory input. Directly drives decisions about cue modality (audio vs. haptic vs. silence), environmental suggestions, and shutdown detection.

| Field | Purpose | Example Values |
|-------|---------|---------------|
| **Auditory sensitivity** | Whether spoken cues at normal volume are comfortable, or whether the system should default to softer/quieter delivery | Typical, Sensitive (reduce volume), Highly sensitive (prefer haptic), Seeks auditory input |
| **Tactile sensitivity** | Whether haptic (vibration) cues are comfortable or aversive | Typical, Sensitive (avoid haptic), Seeks tactile input |
| **Known sensory triggers** | Specific stimuli that reliably cause distress | Loud sudden noises, specific textures, visual clutter, changes in lighting |
| **Calming sensory inputs** | What sensory experiences help this child regulate | Deep pressure, rhythmic sounds, quiet space, specific music, movement |
| **Sensory-seeking behaviors** | Behaviors the child uses to get sensory input they need | Spinning, hand-flapping, jumping, mouthing objects — these are regulatory, not problematic |

### Executive Function Profile

The child's capacity for planning, sequencing, initiating, and sustaining tasks. This determines how many steps the system delivers at once, how aggressively it re-prompts, and how it handles transitions.

| Field | Purpose | Example Values |
|-------|---------|---------------|
| **Working memory capacity** | How many sequential steps the child can hold | 1 step at a time, 2 steps, 3+ steps |
| **Task initiation difficulty** | How much prompting the child needs to start a task they know how to do | Minimal (single cue sufficient), Moderate (needs 2–3 cues), High (needs step-by-step from zero) |
| **Transition tolerance** | How well the child handles shifting from one activity to another | Easy (brief warning sufficient), Moderate (needs countdown + bridging), Difficult (extended warning + first-step anchoring + possible resistance), Highly difficult (transitions regularly trigger meltdowns) |
| **Time awareness** | Whether the child has any functional sense of time passing | Functional (understands "5 minutes"), Limited (understands "soon" vs. "later"), None (time references are meaningless) |
| **Sustained attention capacity** | How long the child can stay on a non-preferred task before attention decays | Brief (1–3 min), Short (3–10 min), Moderate (10–20 min), Extended (20+ min) |
| **Routine dependence** | How much the child relies on predictability and sameness | Flexible, Moderate (prefers routine, tolerates variation), Rigid (deviation causes distress) |

### Emotional Regulation Profile

How the child experiences and manages emotions. This is the profile section that drives meltdown detection, escalation response, calming protocols, and post-incident re-engagement.

| Field | Purpose | Example Values |
|-------|---------|---------------|
| **Baseline heart rate range** | Personalized biometric baseline so the system can detect meaningful deviations | Caregiver-entered or system-learned over first 48 hours |
| **Known meltdown precursors** | Child-specific early warning signs the system should watch for | Pacing, hand-wringing, specific verbal scripts ("I don't like this"), humming escalation, room-hopping |
| **Typical escalation speed** | How quickly the child moves from early warning to full meltdown | Slow (20+ min), Moderate (5–20 min), Fast (< 5 min), Unpredictable |
| **Effective calming strategies** | What works for this child during and after escalation | Deep breathing cues, counting, quiet space, specific music, reduced stimulation, physical compression (caregiver-provided), distraction to preferred topic |
| **Recovery pattern** | What the child needs after a meltdown before re-engagement | Silence + time (N minutes), Gentle verbal reassurance, Physical comfort (caregiver), Immediate distraction to preferred activity |
| **Response to direct praise** | Whether the child is comfortable with explicit positive reinforcement | Enjoys direct praise, Tolerates brief praise, Uncomfortable with praise (use indirect affirmation: "those teeth look clean" instead of "great job brushing") |

### Behavioral Profile

The child's characteristic patterns of behavior — what they do, why, and what works.

| Field | Purpose | Example Values |
|-------|---------|---------------|
| **Attention-seeking pattern** | Whether and how the child seeks attention, and whether negative attention is reinforcing | Does not seek attention actively; Seeks positive attention appropriately; Seeks any attention (negative attention is reinforcing); Escalates when ignored |
| **Perseveration tendencies** | Whether the child gets stuck on topics, questions, or activities | None; Mild (occasional repetition); Moderate (daily perseverative loops); Severe (dominates communication) |
| **Known perseverative topics** | Specific subjects the child fixates on | Dinosaurs, a specific TV show, a specific food, the schedule for tomorrow |
| **Avoidance behaviors** | What the child does to escape non-preferred tasks | Leaves the room, goes limp, starts scripting, asks unrelated questions, provokes sibling |
| **Known reinforcers** | What motivates this child | Specific praise phrases, access to preferred activity, specific foods, stickers/tokens, screen time, verbal acknowledgment of their interests |
| **Sibling dynamics** | Interaction patterns with other children in the household | Only child; Sibling — generally positive; Sibling — frequent conflict; Sibling — target child seeks sibling's attention; Sibling — target child provokes sibling for caregiver attention |

### Interaction Preferences

How the child prefers to be addressed. This is the profile section caregivers often know instinctively but have never been asked to articulate.

| Field | Purpose | Example Values |
|-------|---------|---------------|
| **Prompting style** | Whether the child responds better to directives or suggestions | Directive ("Pick up your toothbrush"), Suggestive ("Want to grab your toothbrush?"), Choice-based ("Blue toothbrush or green toothbrush?") |
| **Tone preference** | What vocal register and energy level the child responds to | Warm and upbeat, Calm and low-energy, Matter-of-fact, Playful |
| **Name use** | Whether the system should use the child's name in cues | Always (helps with attention capture), Sometimes, Rarely (child finds it aversive or ignoring-triggering) |
| **Humor tolerance** | Whether the system can use light humor in cues | Enjoys humor, Tolerates humor, Confused or distressed by humor |
| **Firmness tolerance** | How the child responds to firmer boundary-setting cues | Responds well to gentle firmness, Escalates when firm language is used, Requires firm + warm (firm boundary with immediate warmth) |

---

## Response Protocol Engine

The protocol engine is the decision system at the center of HelperWatch. It takes in signals (what just happened), reads the child's profile (who is this child), evaluates context (what kind of day/moment is this), and selects a response from the library of caregiver-defined protocols.

### Protocol Structure

Every protocol in the system follows the same five-part structure:

**1. Trigger Conditions**
What activates this protocol. Triggers can be:
- **Transcript-based** — A detected pattern in the child's speech (echoed phrase, repeated question, distress keyword, specific known phrase)
- **Biometric-based** — Heart rate crossing a threshold, accelerometer detecting pacing or stillness
- **Location-based** — Room transition, room departure during active routine, arrival in a specific room
- **Time-based** — Scheduled routine start, elapsed time since last interaction, elapsed time on current step
- **Behavioral composite** — A combination of the above (heart rate trending up AND room transitions increasing AND speech volume rising)
- **System-initiated** — Proactive engagement timer fires, attention check-in interval reached

Triggers can be combined with AND/OR logic. A protocol can have multiple trigger paths.

**2. Context Qualifiers**
After a trigger fires, context qualifiers narrow the response selection. The same trigger can produce different responses depending on:

- **Day quality score** — Is today going well or badly? (See "Context Evaluation Model" below.)
- **Recent event buffer** — Did something significant happen recently? (Meltdown 20 minutes ago? Successful routine 10 minutes ago?)
- **Current state** — Is the child calm, agitated, escalating, in shutdown, or in recovery?
- **Active routine** — Is a routine in progress? Which step?
- **Time of day** — Morning, afternoon, evening, bedtime?
- **Profile flags** — Does the child's profile indicate special handling for this type of trigger? (e.g., "time awareness = none" changes how countdown warnings work)

Context qualifiers are evaluated as **if/then branches**: if the day quality score is above threshold X and no recent meltdowns, use response set A; if the day quality score is below threshold Y or a meltdown occurred within the last 30 minutes, use response set B.

**3. Response Actions**
The ordered set of things the system does when the protocol activates. Actions include:
- **Deliver cue** — Speak a specific prompt (from a script or constrained template)
- **Adjust prompt intensity** — Increase or decrease the frequency/complexity of cues
- **Shift modality** — Switch from verbal to haptic, or from haptic to silence
- **Alert caregiver** — Send a push notification with context
- **Suppress responses** — Stop all cue delivery for a specified duration
- **Activate sub-protocol** — Trigger another protocol (e.g., calming protocol within meltdown arc protocol)
- **Log event** — Record this event for trend analysis (if logging is enabled)
- **Update day quality score** — Increment or decrement the running day assessment

**4. Escalation Rules**
What happens if the initial response does not produce the expected outcome. Escalation is graduated:
- **Wait** — Allow a configurable pause for the child to process
- **Rephrase** — Deliver the same intent in simpler or different language
- **Simplify** — Reduce the response to its most basic form
- **Shift strategy** — Move to a fundamentally different approach (e.g., from redirect to acknowledgment)
- **Alert caregiver** — If automated escalation is exhausted, hand off to the human
- **Disengage** — In some cases, the right escalation is to stop trying (e.g., during sensory shutdown)

Escalation rules have a configurable **maximum depth** — the system will not cycle through more than N escalation steps before alerting the caregiver. This prevents infinite loops.

**5. Exit Conditions**
When the protocol deactivates and normal operation resumes:
- **Task completion** — The child completed the prompted step
- **State change** — The child's biometrics returned to baseline, or they moved to a new room
- **Timeout** — A maximum protocol duration has elapsed
- **Caregiver override** — The caregiver manually dismisses the protocol from the app
- **Preemption** — A higher-priority protocol activates and takes over

### Context Evaluation Model

The protocol engine maintains three running assessments that inform every decision:

#### Current State Assessment

A real-time composite of the child's observable condition, updated continuously:

| Signal | Source | Contributes To |
|--------|--------|---------------|
| Heart rate | Watch biometric sensor | Arousal level, meltdown pre-detection |
| Heart rate trend (5-min rolling) | Computed | Escalation trajectory |
| Movement classification | Watch accelerometer | Pacing (agitation), stillness (shutdown or calm), walking (transitioning) |
| Speech activity | Transcript stream | Engagement level, perseveration detection, echolalia detection |
| Room | ESP32 RSSI resolution | Routine context, wandering detection, transition tracking |
| Time on current step | Routine engine | Stuck detection, task abandonment |
| Time since last interaction | System clock | Attention gap detection, proactive engagement trigger |

The current state is not a single number — it is a structured snapshot that protocols query specific fields from.

#### Day Quality Score

A rolling aggregate that answers: "What kind of day is this child having?" This score is critical because the right response to a behavior at 9 AM after a smooth morning is different from the right response to the same behavior at 4 PM after three meltdowns.

The day quality score is a simple accumulator:

| Event | Score Impact |
|-------|-------------|
| Routine step completed independently | +1 |
| Routine completed on time | +3 |
| Proactive engagement responded to positively | +1 |
| Meltdown (full cycle) | −5 |
| Escalation (caught before meltdown) | −2 |
| Sibling conflict detected | −2 |
| Extended task abandonment (>10 min) | −1 |
| Successful transition | +2 |
| Failed transition (resistance) | −2 |

The score is not a grade. It is a heuristic signal that protocols can use to adjust response selection. A child with a high day quality score has earned more latitude — the system can be more playful, less prescriptive, and slower to escalate. A child with a low day quality score is likely dysregulated — the system should be gentler, more patient, quicker to alert the caregiver, and less ambitious about routine completion.

The score resets at a configurable time each day (default: wake-up).

#### Recent Event Buffer

A short-term memory of significant events, with time decay:

| Event | Decay Window | Influence |
|-------|-------------|-----------|
| Meltdown occurred | 60 minutes | Reduce prompt intensity, lower escalation thresholds, prefer calming protocols |
| Sibling conflict | 30 minutes | Suppress routine prompts if child is still in the same room, heighten caregiver alert sensitivity |
| Successful routine completion | 30 minutes | Allow more autonomy, delay re-prompting, increase proactive engagement warmth |
| Caregiver intercom injection | 15 minutes | Integrate caregiver's intent, avoid contradicting recent parent message |
| Transition failure | 45 minutes | On next transition, use extended countdown + bridging cues regardless of profile defaults |

Events in the buffer influence protocol selection within their decay window, then fade. The buffer prevents the system from treating each moment in isolation — a child who melted down 20 minutes ago should not be prompted with the same intensity as a child who completed a routine 5 minutes ago, even if their current biometrics are identical.

### Protocol Categories

The following categories map directly to the user stories in [Behavioral Use Cases](Behavioral%20Use%20Cases.md). Each category contains multiple protocols that cover different context branches.

#### Echolalia Response Protocols (US-1)

| Context | Protocol |
|---------|----------|
| Child echoes a routine cue, first occurrence | Acknowledge echo, rephrase instruction with concrete anchor |
| Child echoes repeatedly, no task progress | Simplify to single physical action, increase wait time per profile latency |
| Child echoes persistently, biometrics stable | Reduce to minimal prompt, notify caregiver — possible processing difficulty |
| Child echoes persistently, biometrics rising | Activate calming sub-protocol — echolalia may be stress response |
| Child is scripting (known media/memorized), no routine active | Do nothing — scripting is self-regulatory |
| Child is scripting, routine active, within delay threshold | Wait — allow self-regulation before re-engaging |
| Child is scripting, routine active, past delay threshold | Gentle interject: "When you're ready, [next step]" |

#### Perseveration Response Protocols (US-2)

| Context | Protocol |
|---------|----------|
| Repeated question, count ≤ N₁, day quality high | Answer naturally, full patience |
| Repeated question, count ≤ N₁, day quality low | Answer naturally but add brief comfort: "Yep, chicken nuggets. You're doing great today." |
| Repeated question, count N₁–N₂ | Redirect: answer briefly + pivot to current activity or preferred topic |
| Repeated question, count N₂–N₃ | Acknowledge without re-answering: "I know you're thinking about that. It'll be time soon." |
| Repeated question, count > N₃, biometrics stable | Minimal acknowledgment ("Mm-hmm"), alert caregiver |
| Repeated question, count > N₃, biometrics rising | Shift to calming protocol — perseveration is likely anxiety-driven |
| Known perseverative topic (from profile), not a question | Engage briefly with the topic (validate interest), then gently redirect if routine is active |

#### Attention and Engagement Protocols (US-3)

| Context | Protocol |
|---------|----------|
| Proactive check-in timer fires, child is calm | Warm engagement bid: "What are you up to?" / "Those look cool — tell me about them" |
| Child responds positively to check-in | Sustained follow-up (1–3 exchanges), then graceful exit |
| Child ignores check-in | Log non-response, retry in shorter interval |
| Escalation pre-detection (biometrics + movement trending up) | Preemptive engagement: "Hey, I was thinking about you. What's going on?" |
| Post-outburst, child calm, recovery buffer elapsed | Gentle re-engagement: "That was tough. Want to tell me about it, or just hang out?" |
| Attention-seeking behavior detected (profile flag + provocative transcript) | Do not react to the provocation; redirect to preferred topic or offer engagement on child's terms |

#### Transition Protocols (US-4)

| Context | Protocol |
|---------|----------|
| Scheduled transition, profile = easy transitions | Brief warning: "5 minutes until [next activity]" → "Time for [next activity]" |
| Scheduled transition, profile = moderate transitions | Extended countdown: 10 → 5 → 2 → 1 minute warnings with bridging cues |
| Scheduled transition, profile = difficult transitions | Extended countdown + bridging + first-step anchoring: "Can you stand up?" |
| Transition warning delivered, child has not moved, time elapsed | Room-aware follow-up: "Hey, [next activity] is in the [room]. Can you head over?" |
| Transition warning delivered, child vocally refuses | De-escalate: "Okay, take a second." Brief pause. Re-approach with first-step anchor. |
| Transition warning delivered, biometrics spike | Shift to calming sub-protocol. Alert caregiver. Do not force transition. |
| Recent event buffer contains transition failure | On next transition, automatically use most conservative protocol regardless of profile default |

#### Task Re-Engagement Protocols (US-5)

| Context | Protocol |
|---------|----------|
| Child leaves routine room, timer < threshold | Monitor silently — child may be getting water, using bathroom |
| Child leaves routine room, timer ≥ threshold | Gentle redirect: "Your [task item] is waiting in the [room]. Can you head back?" |
| Child returns to routine room | Re-anchor: "Welcome back! You were [doing X]. Let's keep going." |
| Child returns but does not resume task | Re-prompt with simplified first step |
| Same routine step abandoned repeatedly (trend data) | Flag to caregiver: "This step is abandoned frequently — consider breaking it down" |

#### Routine Scaffolding Protocols (US-6)

| Context | Protocol |
|---------|----------|
| Routine step active, child in correct room, no speech/movement for N seconds | Re-prompt: "Next step is [X]. Can you do that?" |
| Routine step completed (time/verbal/transition confirmation) | Micro-affirmation + advance: "Nice! Now let's [next step]." |
| Step completed independently (before system prompted) | Fading note: reduce prompting for this step next time |
| Step duration exceeds expected time | Stuck detection: "Need help? Your next step is [X]." |
| All steps completed | Routine completion celebration: "You did it! [Routine] is done." |

#### Meltdown Arc Protocols (US-7)

| Context | Protocol |
|---------|----------|
| Build-up detected (multi-signal trend, not yet critical) | Early intervention: calming cue, sensory suggestion, or distraction to preferred topic. Caregiver early warning notification. |
| Build-up intensifying despite intervention | Shift to minimal interaction. Caregiver escalated notification: "Intervention isn't working — you may need to step in." |
| Peak detected (biometric spike + erratic movement OR complete stillness + non-responsiveness) | **Suppress all verbal cues.** Shift to configured peak response (haptic-only, silence, or caregiver-defined). Continuous caregiver notification. |
| Peak subsiding (biometrics trending toward baseline) | Maintain silence. Do not re-engage yet. |
| Recovery buffer elapsed, biometrics stable | Gentle re-engagement: "That was hard. You're okay." Do NOT immediately resume routine. |
| Caregiver triggers routine resume | Resume routine from the interrupted step, not from the beginning. Use gentler prompting intensity. |

#### Sensory Overload Protocols (US-8)

| Context | Protocol |
|---------|----------|
| Shutdown indicators detected (silence + stillness + non-responsiveness) | Suppress all verbal cues immediately. Shift to configured shutdown response mode. |
| Shutdown persists > configured duration | Caregiver notification: "[Child] may be in sensory overload — non-responsive for [X] minutes." |
| Shutdown resolving (micro-movements returning, heart rate normalizing) | Gradual re-engagement: single soft tone or minimal haptic, then pause. Wait for response before any verbal cue. |

#### Conflict Protocols (US-9)

| Context | Protocol |
|---------|----------|
| Multi-voice distress detected (shouting + elevated biometrics) | Suppress all routine prompts. Alert caregiver: "Possible conflict in [room]." |
| Conflict subsides, child still in room | Pause. Do not immediately re-prompt. Monitor for stabilization. |
| Conflict subsides, recovery buffer elapsed | Gentle re-engagement or redirect to calming activity, per caregiver configuration. |

#### Bedtime Protocols (US-10)

| Context | Protocol |
|---------|----------|
| Bedtime macro activated | Shift profile: softer tone, lower energy, adjusted biometric thresholds. Begin wind-down routine. |
| Wind-down routine step active | Standard scaffolding protocol, bedtime tone. |
| Child in bed, room exit detected (1st time) | Warm redirect: "It's bedtime — can you head back to your room?" |
| Child in bed, room exit detected (2nd+ time) | Graduated firmness per configuration. Shorter cues. |
| Bedtime delay tactic — water request (1st time) | Allow: "Sure, go get water. Then right back to bed." |
| Bedtime delay tactic — water request (2nd+ time) | Boundary: "You already had water. Time to stay in bed." |
| Bedtime delay tactic — question | Brief acknowledge + defer: "Great question — let's talk about it in the morning." |
| Child in bed, not sleeping, calm | Companion mode: periodic soft presence cue, ambient sound, breathing guide. |

### Protocol Priority and Conflict Resolution

When multiple protocols trigger simultaneously, the system resolves conflicts using a fixed priority hierarchy:

| Priority | Category | Rationale |
|----------|----------|-----------|
| 1 (highest) | Safety / Meltdown peak | Child safety is non-negotiable. All other protocols yield. |
| 2 | Sensory shutdown | A child in shutdown cannot process any other protocol's output. |
| 3 | Conflict | Multi-person situations require response suppression until resolved. |
| 4 | Meltdown build-up / Calming | Active de-escalation preempts routine work. |
| 5 | Transition | Transitions are time-sensitive and high-friction. |
| 6 | Active routine step | The current task the child is working on. |
| 7 | Perseveration / Echolalia response | Important but can wait if higher-priority events are active. |
| 8 | Proactive engagement | Lowest priority — engagement bids are deferred when anything else is happening. |
| 9 (lowest) | Companion / Ambient | Background presence — always yields. |

When a higher-priority protocol activates, lower-priority protocols are **suspended, not cancelled**. When the higher-priority protocol exits, the suspended protocol resumes from where it was interrupted (e.g., a routine step paused by a meltdown resumes at the same step after recovery).

---

## Configuration and Setup

### Why This Is Initially a Consultancy Process

The power of this system is proportional to the quality of its configuration. A child profile with default values and generic protocols will produce generic responses — better than nothing, but far from the "painfully obvious right answer" that an experienced caregiver can identify in seconds.

Building a detailed profile and a comprehensive protocol set for a specific child requires two things:
1. **Deep knowledge of the child** — what works, what does not, what the specific patterns are, what the triggers are. This knowledge lives in the caregiver's head, built over years of daily experience.
2. **Understanding of the system** — how to translate that knowledge into profile fields, protocol triggers, and response scripts. This is system-specific expertise.

In the early stages of the project, the realistic setup process is a guided consultation: a knowledgeable facilitator (project contributor, trained volunteer, clinician, or an experienced caregiver) works with the family to build the initial profile and protocol set. This is analogous to how AAC (augmentative and alternative communication) devices are set up — the device is powerful, but it requires a trained person to configure it for the individual.

Over time, as the system matures:
- The app itself can guide the setup process with structured questions and suggestions.
- Template profiles and protocol sets can reduce the configuration burden.
- The system can learn from usage patterns and suggest refinements.

But the initial deployment of HelperWatch for a new family should assume that setup is a collaborative, human-guided process — not a solo download-and-go experience.

### Progressive Complexity

The system must be usable on day one with minimal configuration, and must grow more capable as the caregiver adds detail over time.

**Day-one minimum viable profile:**
- Child's name
- Primary communication mode (verbal / limited verbal / non-verbal)
- Working memory capacity (1-step / 2-step / multi-step)
- One basic routine (e.g., morning routine with 5–8 steps)

This is enough to run the core prompting loop with reasonable defaults for everything else. The system uses conservative defaults for unspecified fields: moderate prompting frequency, standard response latency, generic micro-affirmations, meltdown detection on biometric thresholds only.

**Week-one refinement:**
The caregiver adds:
- Sensory sensitivities
- Known meltdown precursors
- Transition tolerance level
- 2–3 additional routines

**Month-one depth:**
- Full emotional regulation profile
- Perseveration patterns and configured thresholds
- Attention-seeking profile and proactive engagement schedule
- Custom echo response scripts
- Sibling context
- Bedtime protocol configuration

**Ongoing:**
- Trend data surfaces suggestions: "Your child abandons the tooth-brushing step 80% of the time. Would you like to break it into sub-steps?"
- The system proposes protocol refinements based on observed outcomes: "Redirecting during perseveration at phase 2 seems to increase repetition count. Would you like to extend the answer phase?"

### Template Profiles

Pre-built profile templates provide reasonable starting points for common presentations. Templates are not diagnoses — they are starting configurations that match commonly observed behavioral patterns.

| Template | Description | Key Defaults |
|----------|-------------|-------------|
| **Non-verbal, severe executive dysfunction** | Child does not use spoken language, needs 1-step concrete prompts, high task initiation difficulty, rigid routine dependence | Haptic-first cues, extended response latency, maximum scaffolding, conservative fading |
| **Verbal, impulsive, attention-seeking** | Child speaks fluently but blurts, interrupts, escalates for engagement, moderate executive dysfunction | Proactive engagement at short intervals, brief acknowledgment of bids, redirect-heavy protocols, firm + warm boundaries |
| **Verbal, anxious, perseverative** | Child speaks but fixates on topics/questions, high anxiety, moderate executive dysfunction | Extended answer phase for perseveration, gentle tone, slow transitions, calming protocols prioritized |
| **Limited verbal, sensory-sensitive** | Child uses some words/phrases, strong sensory sensitivities, moderate-to-severe executive dysfunction | Low-volume audio or haptic-only cues, extended processing time, sensory overload protocols on sensitive thresholds |

Templates populate all profile fields with defaults. The caregiver can then override any field. The template is a starting point, not a prescription.

### Protocol Templates

Pre-built protocol sets mapped to the behavioral categories. These provide a full set of working protocols that the caregiver can customize or replace.

Each protocol template includes:
- Default trigger conditions
- Default context qualifiers with conservative thresholds
- Default response scripts (generic but functional)
- Default escalation rules
- Default exit conditions

The caregiver's primary configuration task is replacing the generic response scripts with child-specific language. The structural logic (triggers, qualifiers, escalation) can often remain at defaults until the caregiver has enough experience to refine them.

---

## Worked Examples

### Example 1: Perseverative Question on a Good Day

**Child profile (relevant fields):**
- Verbal expression: Full sentences
- Time awareness: None
- Perseveration tendency: Severe
- Known perseverative topics: Dinner, tomorrow's schedule
- Day quality score: +8 (good morning, two routines completed successfully)

**Situation:** It is 2:30 PM. The child asks "What's for dinner?" This is the 6th time in the last 40 minutes. The caregiver has configured N₁=5, N₂=12, N₃=20. The child's configured answer for this question is "Chicken nuggets and carrots."

**Protocol trace:**
1. **Trigger:** Repeated question detected. "What's for dinner?" matches the perseveration registry. Repetition count = 6.
2. **Context qualifier:** Count (6) > N₁ (5), so we are in the **redirect phase**. Day quality score is +8 (high). No recent meltdowns in the event buffer.
3. **Response selection:** Because the day is going well, the redirect is warm and light: *"Yep, still chicken nuggets and carrots! Hey, what are you building with those blocks?"*
4. **Escalation rule:** If the child ignores the redirect and asks again within 2 minutes, increment count to 7, remain in redirect phase, vary the redirect topic.
5. **Exit condition:** If the child engages with the redirect topic or 30 minutes pass without another repetition, the repetition counter resets.

**Why the day quality score matters:** If this same child asked the same question for the 6th time on a day with a score of −4 (bad morning, meltdown 2 hours ago), the protocol would select a gentler redirect — less playful, more soothing — and would lower the threshold for shifting to the acknowledge-and-hold phase, because the perseveration is more likely to be anxiety-driven on a bad day.

### Example 2: Question Blurted During a Transition Countdown

**Child profile (relevant fields):**
- Verbal expression: Full sentences
- Task initiation difficulty: High
- Transition tolerance: Difficult
- Auditory processing latency: Moderate (5–10s)
- Attention-seeking pattern: Seeks any attention
- Avoidance behaviors: Asks unrelated questions

**Situation:** The system delivered a "2 minutes until dinner" countdown warning 30 seconds ago. The child is in the living room. They say: "Can I have ice cream tomorrow?"

**Protocol trace:**
1. **Trigger:** Transcript received: "Can I have ice cream tomorrow?"
2. **Context qualifier:** A transition countdown is active (recent event buffer: transition warning 30 seconds ago). The child's profile flags "asks unrelated questions" as an avoidance behavior. The question is not a known perseverative topic — it is a novel bid.
3. **Protocol selection:** The transition protocol is active at priority 5. The question matches the avoidance behavior pattern. The system classifies this as a **transition-avoidance bid**, not a genuine question requiring a full answer.
4. **Response:** Brief acknowledgment without engaging the topic: *"Maybe! We can talk about that after dinner. Right now, one more minute of play time."* The transition countdown is **not reset** by the question. The system maintains the countdown cadence.
5. **Escalation rule:** If the child asks another avoidance question, the system acknowledges even more briefly: *"Let's talk after dinner."* and continues the countdown.
6. **Exit condition:** The transition protocol remains active until the child moves to the kitchen or the caregiver overrides.

**Why the profile matters:** A different child — one whose profile does not flag question-asking as avoidance behavior and whose perseveration tendency is high — would receive a full, patient answer to the ice cream question, because for that child, an unanswered question can become a perseverative loop that makes the transition harder, not easier.

### Example 3: Sibling Conflict Followed by Post-Conflict Meltdown

**Child profile (relevant fields):**
- Sibling dynamics: Sibling — frequent conflict
- Escalation speed: Fast (<5 min)
- Known meltdown precursors: Shouting, pacing, "I hate you" phrase
- Effective calming strategies: Quiet space, reduced stimulation
- Recovery pattern: Silence + time (10 min)
- Baseline heart rate: 80 bpm

**Situation:** It is 3:15 PM. The child and their sibling are in the living room. The system detects multi-voice shouting, the child's heart rate jumps from 82 to 110 bpm, and the transcript includes "I hate you."

**Protocol trace:**
1. **Trigger:** Multi-voice distress detected (conflict protocol trigger) AND "I hate you" phrase detected (profile-specific meltdown precursor) AND heart rate spike (+28 bpm).
2. **Priority resolution:** Conflict protocol (priority 3) and meltdown build-up protocol (priority 4) both trigger. Conflict protocol takes priority. The routine scaffolding protocol (priority 6, afternoon activity) is suspended.
3. **Response — conflict phase:** All routine prompts suppressed. Caregiver alert sent immediately: *"Conflict detected in living room. [Child]'s heart rate at 110 bpm. Meltdown precursor phrase detected."*
4. **Transition — conflict subsides, meltdown begins:** Shouting stops, but the child's heart rate continues rising to 125 bpm and the accelerometer shows pacing. The conflict protocol yields to the meltdown build-up protocol (priority 4).
5. **Response — meltdown build-up:** The system attempts early calming: *"Hey, let's take some breaths."* But the child's escalation speed is flagged as fast. Heart rate hits 135 bpm. The system detects peak onset.
6. **Response — meltdown peak:** All verbal cues suppressed. System shifts to silence (per profile: calming strategy = quiet space + reduced stimulation). Caregiver receives continuous biometric updates.
7. **Response — recovery:** Heart rate returns to 95 bpm, movement stabilizes. The system starts a 10-minute recovery buffer (per profile: recovery = silence + time, 10 min). No cues during this window.
8. **Post-recovery re-engagement:** After the 10-minute buffer, heart rate is 84 bpm. System delivers gentle re-engagement: *"That was tough. You're okay."* Pauses. Does not resume the afternoon routine until the caregiver triggers it.
9. **Day quality score update:** Sibling conflict (−2) + meltdown (−5) = −7 total impact. The score drops from +4 to −3. All subsequent protocol selections for the rest of the day will use low-day-quality branches: gentler tone, lower expectations, quicker caregiver alerts.

---

## Related Documents

- [Behavioral Use Cases](Behavioral%20Use%20Cases.md) — The scenarios this architecture is designed to handle
- [System Architecture](System%20Architecture.md) — How the protocol engine fits into the overall system
- [Cloud Backend](Cloud%20Backend.md) — Where the protocol engine runs (AI orchestration layer)
- [Caregiver Mobile App](Caregiver%20Mobile%20App.md) — Where profiles are configured and protocols are managed
- [Wearable App](Wearable%20App.md) — The device delivering cues and capturing signals
- [Safety and Ethics Overview](../ethics/Safety%20and%20Ethics%20Overview.md) — Ethical framework governing protocol design
- [Mission and Values](../project/Mission%20and%20Values.md) — Why this system exists
