# Behavioral Use Cases

## Purpose

The HelperWatch architecture documents describe *what* the system does — room detection, audio transcription, LLM classification, cue playback. This document describes *why* it does those things, grounded in the specific behavioral realities of the children and families the system is designed to serve.

Features built in the abstract tend to solve abstract problems. The children who will wear this device do not have abstract problems. They have a morning routine that takes 90 minutes instead of 20. They have a question they need answered 40 times before lunch. They have a tower of Legos that somebody — anybody — needs to acknowledge as genuinely, unequivocally cool, or the next hour will be unbearable for everyone in the house.

Each user story in this document describes a real behavioral scenario, explains what it demands from caregivers today, defines how HelperWatch should respond, and maps the scenario to concrete feature requirements. These stories directly inform the [Adaptive Response Architecture](Adaptive%20Response%20Architecture.md) — the child profiles, protocol engine, and context-aware decision logic that determine how the system responds in any given moment.

---

## User Stories

### US-1: Echolalia and Scripted Speech

**The scenario.** The child repeats phrases — sometimes echoing what was just said to them (immediate echolalia), sometimes reciting lines from a show, a book, or a past conversation (delayed echolalia / scripting). A caregiver says "time to brush your teeth," and the child responds "time to brush your teeth" without moving. Or the child walks through the house reciting dialogue from a cartoon, seemingly disconnected from the current context.

**Why it happens.** Echolalia is not random noise. For many children, it serves communicative functions: self-regulation, processing, assent, protest, or a request that the child cannot yet formulate in original language. A child echoing "time to brush your teeth" may be acknowledging the instruction, or may be using the repetition to process it. A child scripting cartoon dialogue may be self-soothing, or may be attempting to communicate something that maps, in their internal logic, to the quoted scene.

**Current caregiver burden.** The caregiver must constantly distinguish between echolalia-as-acknowledgment and echolalia-as-non-comprehension — and respond differently to each. This is cognitively exhausting, especially when the same phrase is echoed dozens of times per day. Many caregivers default to repeating the instruction louder or more firmly, which often escalates the situation without improving comprehension.

**HelperWatch response strategy.**

- **Transcript classification.** The LLM classifier must be able to identify echoed speech — cases where the child's transcript closely or exactly matches a recent system cue or a known caregiver phrase. This is a classification category, not a failure state.
- **Echo-aware response selection.** When echolalia is detected, the system should not simply repeat the same cue louder. It should:
  1. **First echo:** Acknowledge and gently rephrase. *"That's right — let's brush teeth. Can you pick up your toothbrush?"* (Break the instruction into a smaller, more concrete first step.)
  2. **Repeated echoes without task progress:** Shift to a simpler prompt with a more concrete anchor. *"Your toothbrush is the blue one. Can you grab it?"*
  3. **Persistent echoing with no engagement:** Reduce to minimal prompting and notify the caregiver. The child may be dysregulated, not non-compliant.
- **Scripting recognition.** When the child is reciting known scripts (media dialogue, memorized phrases) outside of any routine context, the system should generally leave them alone — scripting is often self-regulatory. If a routine is active and the child is scripting instead of engaging, the system can gently interject after a configurable delay: *"I love that part too. When you're ready, your next step is [X]."*
- **Caregiver-configured echo scripts.** Caregivers should be able to define custom responses to known echoed phrases, since they understand the communicative intent behind their child's specific echolalia patterns better than any model can.

**Feature requirements:**
- Transcript echo detection (compare child transcript to recent cue history)
- Graduated response escalation (rephrase → simplify → reduce → alert)
- Scripting-vs-routine conflict detection
- Caregiver-defined echo response overrides
- Configurable delay before interrupting self-regulatory scripting

---

### US-2: Perseverative Questioning

**The scenario.** The child asks the same question repeatedly — sometimes dozens of times within an hour. *"What's for dinner?" "What's for dinner?" "What's for dinner?"* The question has been answered. The answer has not changed. The child asks again.

**Why it happens.** Perseverative questioning is rarely about information. It can be driven by anxiety (the answer provides temporary relief, but the relief fades quickly), a need for predictability and control (the ritual of asking and receiving a consistent answer is soothing), difficulty with working memory (the answer genuinely does not stick), or a bid for interaction and connection (the question is a social tool, not an informational one).

**Current caregiver burden.** This is among the most grinding daily experiences for caregivers. The 40th repetition of the same question erodes patience in a way that is difficult to describe to someone who has not lived it. Caregivers oscillate between answering patiently (which can reinforce the loop), refusing to answer (which can trigger escalation), and snapping (which the caregiver then feels guilty about). There is no good option available at repetition number 40.

**HelperWatch response strategy.**

The system should implement a **graduated response strategy** with caregiver-configurable thresholds:

1. **Answer phase (repetitions 1–N₁).** Answer the question naturally and patiently, every time. The system does not get tired. *"Dinner is going to be chicken nuggets."* This phase absorbs the repetitions that the caregiver currently endures.
2. **Redirect phase (repetitions N₁–N₂).** Begin gently redirecting toward the current activity or a preferred topic. *"Yep, still chicken nuggets! Hey, what are you building over there?"* The redirect is warm, not dismissive.
3. **Acknowledge-and-hold phase (repetitions N₂–N₃).** Acknowledge the question without re-answering. *"I know you're thinking about dinner. It'll be time soon. Right now, let's focus on [current activity]."* This holds a gentle boundary without ignoring the child.
4. **Boundary phase (repetitions > N₃).** If the perseveration continues past the configured threshold, the system can: reduce to minimal acknowledgment (*"Mm-hmm"*), shift to a calming routine, or alert the caregiver that the perseveration may be anxiety-driven and may need human intervention.

**Configurable parameters:**
- `N₁`, `N₂`, `N₃` thresholds (caregiver sets these based on their child's patterns)
- Time window for repetition counting (e.g., reset after 30 minutes of no repetition)
- Per-question response overrides (caregiver can define the exact answer for known perseverative questions)
- Escalation action at boundary phase (calm-down cue, caregiver alert, or both)

**Ethical note.** The system should never gaslight or contradict a prior answer. If the answer to "what's for dinner" was "chicken nuggets," the system must not later say "I'm not sure" or "we'll see" to try to break the loop. Consistency is not optional for this population — inconsistency causes more anxiety than repetition does.

**Feature requirements:**
- Repetition detection with configurable time window
- Graduated multi-phase response strategy
- Per-question caregiver-defined answer registry
- Configurable phase thresholds
- Caregiver alert at boundary phase
- Answer consistency enforcement (no contradictions across phases)

---

### US-3: Attention-Seeking Escalation

**The scenario.** The child is starved for engagement. Not because the caregivers are neglectful — but because there are other children, meals to cook, work to do, and the caregiver simply cannot provide a continuous stream of focused, validating attention for 14 waking hours. The child has learned, through lived experience, that negative attention (yelling, scolding, being chased around the house) is more reliably available than positive attention. So they escalate: throwing things, provoking siblings, screaming, knocking objects off tables — not because they enjoy the chaos, but because the chaos guarantees a response.

**Why it happens.** For many children in the target population, the drive for human connection and validation is intact even when the social skills to obtain it appropriately are not. The child cannot say "I feel invisible and I need someone to notice me." They can throw a cup across the kitchen. The cup always works.

**Current caregiver burden.** The caregiver is trapped in a reinforcement loop they understand intellectually but cannot escape practically. Ignoring the behavior (extinction) is the textbook recommendation, but it is nearly impossible to execute when the child is destroying property, hitting siblings, or putting themselves in danger. And extinction bursts — where the behavior intensifies before it improves — can last weeks, during which the household is in crisis.

**HelperWatch response strategy.**

This is the use case that demands the most from the system and raises the most significant ethical questions. The core strategy is **proactive validating engagement** — providing the attention the child needs *before* the escalation cycle begins.

- **Proactive check-ins.** The system initiates unprompted, warm engagement at configurable intervals: *"Hey, those Legos are looking really cool. Tell me about what you're building."* These are not routine prompts — they are social bids. Their purpose is to fill the attention vacuum before the child fills it with chaos.
- **Sustained interest.** When the child responds to a check-in (describes their Legos, tells a story, shares something), the system responds with genuine-sounding follow-up: *"Whoa, that's a tall tower! What are you going to put on top?"* This is not a single affirmation and move on — it is a brief, sustained interaction.
- **Escalation circuit-breaker.** If biometric or behavioral signals indicate the beginning of an escalation arc (increased movement, elevated heart rate, room changes without purpose), the system can preemptively engage: *"Hey, I was just thinking about you. What are you up to right now?"*
- **Post-incident re-engagement.** After an outburst, the system does not lecture or punish. It waits for the child to calm (biometric return to baseline, movement stabilizes) and then re-engages warmly: *"Sounds like that was tough. Want to tell me about what happened, or should we just hang out for a bit?"*
- **Caregiver "attention budget" configuration.** The caregiver can configure how frequently and how extensively the system provides proactive engagement. Some children need a check-in every 10 minutes; others every 30. The caregiver knows their child.

**Feature requirements:**
- Proactive (system-initiated) engagement cues, not just reactive prompting
- Configurable check-in interval and engagement duration
- Follow-up response generation (constrained, not freeform — but contextual to the child's response)
- Escalation pre-detection (biometric + behavioral pattern matching)
- Post-incident cool-down and re-engagement protocol
- Caregiver-configurable attention schedule

---

### US-4: Transition Resistance

**The scenario.** The child is playing with blocks. It is time to eat dinner. The caregiver announces the transition. The child does not transition. The caregiver announces again. And again. The child melts down, or digs in, or runs to another room. What should be a 30-second shift in activity becomes a 20-minute confrontation.

**Why it happens.** Transitions require multiple simultaneous executive functions: disengaging from a current activity (inhibition), holding the new activity in mind (working memory), initiating a new sequence of actions (task initiation), and tolerating the emotional discomfort of leaving something preferred for something less preferred (emotional regulation). For children with severe executive dysfunction, each of these is independently difficult. Together, they are often overwhelming.

**Current caregiver burden.** Caregivers report that transitions are the single highest-friction point of the day. The unpredictability of which transitions will be easy and which will trigger a meltdown makes planning nearly impossible. Caregivers compensate by providing extensive warnings, using timers, offering bribes, or simply avoiding transitions altogether — all of which have diminishing returns.

**HelperWatch response strategy.**

- **Countdown warning sequence.** The system delivers multi-step advance warnings before any scheduled transition: *"In 10 minutes, it'll be time for dinner." → "5 more minutes of block time, then dinner." → "2 more minutes." → "Okay, last minute — let's start putting blocks away."* The intervals and language are caregiver-configurable.
- **Bridging cues.** Rather than an abrupt "stop X, start Y," the system bridges: *"Your blocks will be right here when you get back after dinner. Let's go see what's cooking."* The bridge validates the current activity and connects it to the future, reducing the sense of loss.
- **First-step anchoring.** Instead of announcing the entire transition ("go to the kitchen and sit at the table and eat"), the system gives only the first physical action: *"Can you stand up?"* Then: *"Now let's walk to the kitchen."* Then: *"Find your seat."* Each step is small enough to be non-threatening.
- **Transition macro with room awareness.** When the caregiver triggers a transition macro (e.g., "Dinner"), the system combines the countdown sequence with room-aware re-engagement if the child does not move. If the child remains in the bedroom 3 minutes after the final warning, the system re-engages: *"I bet dinner smells good. Can you head to the kitchen?"*
- **Resistance detection and de-escalation.** If the child vocally refuses or biometrics spike during a transition, the system does not escalate pressure. It backs off to a holding pattern: *"Okay, take a second. We'll try again in a minute."* The caregiver is notified.

**Feature requirements:**
- Multi-step configurable countdown warning sequences
- Bridging cue templates (current activity → next activity)
- First-step decomposition prompts
- Room-aware transition follow-up (child expected in room X, still in room Y)
- Resistance detection (vocal refusal in transcript + biometric spike)
- De-escalation fallback with caregiver alert
- Transition macro triggers in the caregiver app

---

### US-5: Task Abandonment and Wandering

**The scenario.** The child is halfway through brushing their teeth. They put the toothbrush down, walk out of the bathroom, and go to the living room. They are now standing in the living room doing nothing in particular, or they have picked up a toy. The tooth-brushing routine is suspended mid-step.

**Why it happens.** Task abandonment is a hallmark of severe executive dysfunction. The child's working memory may have simply dropped the task — they are not refusing, they have genuinely lost the thread. Alternatively, they may have been pulled away by a sensory stimulus (a sound, a visual), or they may have reached a step in the routine that they find aversive and disengaged to avoid it.

**Current caregiver burden.** The caregiver must physically track the child, redirect them back to the task, and re-establish context ("you were brushing your teeth, remember?"). This happens dozens of times per day across multiple routines. It is one of the primary reasons caregivers describe themselves as "human GPS units."

**HelperWatch response strategy.**

- **Room-departure detection.** When the child leaves the room where a routine step is active, the system detects the room transition and begins a configurable timer.
- **Gentle redirect.** After the timer expires (e.g., 60 seconds), the system issues a re-engagement cue: *"Hey, your toothbrush is waiting for you in the bathroom. Can you head back?"*
- **Contextual re-anchoring.** When the child returns to the routine room, the system restates the specific step they were on: *"Welcome back! You were brushing your teeth — keep going, you're almost done."*
- **Pause-and-resume logic.** The routine engine must support a "paused" state for interrupted routines. When a routine is paused, the step counter does not advance, and the system tracks the duration of the pause. Prolonged pauses trigger caregiver notification.
- **Aversion detection.** If the child consistently abandons the same routine at the same step, the system flags this pattern to the caregiver via trend reporting. This may indicate that a specific step needs to be broken into smaller sub-steps, or that the child needs direct human support at that point.

**Feature requirements:**
- Room-departure detection during active routines
- Configurable re-engagement delay timer
- Re-anchoring cues on room return (restate current step)
- Routine pause/resume state management
- Repeated-abandonment pattern detection and caregiver reporting
- Step-level aversion flagging in trend data

---

### US-6: Morning Routine Autonomy

**The scenario.** The child needs to wake up, get out of bed, use the toilet, brush teeth, get dressed, eat breakfast, and get to the door with shoes and backpack. Each of these is a multi-step task. The entire sequence takes 20 minutes for a neurotypical child. It takes 60–90 minutes for this child, with continuous verbal prompting at every step.

**Why it happens.** The morning routine is the canonical executive function challenge: a long, multi-step sequence performed under time pressure, with low intrinsic motivation (the child does not want to go to school), and high caregiver stress (the bus is coming). Every sub-skill that executive dysfunction impairs — task initiation, sequencing, working memory, time awareness, emotional regulation — is exercised simultaneously.

**Current caregiver burden.** This is the use case that most caregivers describe first when explaining why they need help. The morning routine sets the emotional tone for the entire day. A failed morning means a dysregulated child at school, a frustrated caregiver at work, and a household already in deficit before 8 AM.

**HelperWatch response strategy.**

This is the "canonical" prompting loop — the use case the system architecture was originally designed around.

- **Room-aware step advancement.** The routine engine tracks which room the child is in and which step is active. When the child enters the bathroom, the system begins the hygiene sub-routine. When they enter the bedroom, it shifts to dressing.
- **Step-by-step prompting.** Each step is delivered individually: *"First, pick up your toothbrush."* The system waits for completion signals (time elapsed, child's verbal confirmation, or room transition) before advancing.
- **Micro-affirmations.** After each completed step: *"Nice job! Teeth are done. Now let's get dressed."* These are brief, warm, and consistent — not overwrought.
- **Time awareness cues.** At configurable intervals, the system provides gentle time context: *"You're doing great. The bus comes in 20 minutes."*
- **Dynamic fading.** As the child demonstrates mastery of specific steps (completes them independently within the expected time window over multiple days), the system reduces prompting for those steps. A child who has mastered tooth-brushing stops receiving step-by-step dental prompts and instead gets: *"Go ahead and brush your teeth — I know you've got this."*
- **Stuck detection.** If a step has been active for longer than its expected duration, the system re-prompts or simplifies: *"You've been in the bathroom for a bit. Need help with anything? Your next step is to pick up your toothbrush."*

**Feature requirements:**
- Multi-step routine definition with per-step expected duration
- Room-aware step advancement and sub-routine activation
- Micro-affirmation cue library (varied, not identical each time)
- Configurable time awareness cues
- Dynamic fading engine with per-step mastery tracking
- Stuck/timeout detection with re-prompt or simplification

---

### US-7: Meltdown Arc (Full Cycle)

**The scenario.** The existing documentation describes "meltdown intercept" as a biometric spike detection feature. In practice, a meltdown is not a spike — it is an arc with a build-up phase (minutes to hours), a peak (seconds to minutes), and a recovery phase (minutes to hours). Catching only the spike means intervening too late. The system must understand and respond to the full cycle.

**The build-up.** The child becomes increasingly agitated. Heart rate drifts upward. Movement becomes more erratic — pacing, fidgeting, restless room transitions. Speech may become louder, more repetitive, or more fragmented. The child may start engaging in behaviors that are early warning signs specific to them (a particular verbal script, a specific physical pattern). This phase can last 5 minutes or 2 hours.

**The peak.** The child is in full dysregulation: screaming, crying, throwing things, hitting, self-injurious behavior, or complete shutdown (non-verbal, non-responsive, frozen). The child is not making choices during this phase — they are in neurological crisis. No verbal cue from any source (human or machine) is likely to be processed during the peak.

**The recovery.** The storm passes. The child is exhausted, often embarrassed or confused. They may be non-verbal. They may seek comfort. They may need 20 minutes of silence. They may need to hear that they are not in trouble.

**Current caregiver burden.** Caregivers learn to read the build-up signs — but only after years of pattern recognition, and even then, they catch it too late roughly half the time. During the peak, caregivers are in crisis management mode: ensuring physical safety, protecting siblings, absorbing the emotional blast. During recovery, they are often too drained to provide the calm, validating re-engagement that the child needs.

**HelperWatch response strategy.**

- **Build-up detection.** The system tracks biometric and behavioral signals over a rolling window (not just instantaneous spikes):
  - Heart rate trending upward over 5–10 minutes (not just a single high reading)
  - Accelerometer showing increasing agitation (pacing, erratic movement)
  - Room transition frequency increasing (restless wandering)
  - Transcript indicators (increased volume, repetition, specific caregiver-flagged phrases)
- **Early intervention.** During the build-up, before the peak: calming cues (*"Hey, let's take a few deep breaths together. In... and out..."*), sensory suggestions (*"Want to go to your quiet spot for a few minutes?"*), or caregiver-configured calming scripts.
- **Caregiver early warning.** A push notification during the build-up — not at the peak. *"[Child]'s heart rate and movement have been trending up for the last 10 minutes. You may want to check in."* This gives the caregiver lead time to intervene while intervention can still help.
- **Peak protocol.** During peak dysregulation, the system should **reduce or cease verbal prompting** entirely. A child in neurological crisis does not benefit from a voice in their ear saying "calm down." The system shifts to:
  - Minimal, slow haptic patterns (if configured as calming by the caregiver)
  - Silence (if the caregiver has configured silence as the peak response)
  - Continuous caregiver notification with biometric updates
- **Recovery re-engagement.** When biometrics return toward baseline and movement stabilizes, the system waits a configurable buffer period, then gently re-engages: *"That was hard. You're okay. Take your time."* It does not immediately restart the interrupted routine. The caregiver can trigger routine resumption when they judge the child is ready.

**Feature requirements:**
- Rolling-window biometric trend analysis (not just threshold triggers)
- Multi-signal build-up scoring (heart rate + accelerometer + room transitions + transcript cues)
- Caregiver-configurable build-up indicator phrases
- Early warning push notification with lead time
- Peak detection with automatic prompt suppression
- Configurable peak response mode (haptic-only, silence, or caregiver-defined)
- Recovery detection (biometric return to baseline + movement stabilization)
- Configurable recovery buffer before re-engagement
- Post-meltdown re-engagement scripts (gentle, non-punitive)
- Meltdown logging for trend analysis (frequency, duration, precursor patterns)

---

### US-8: Sensory Overload and Shutdown

**The scenario.** The environment becomes too loud, too bright, too chaotic, or too unpredictable. The child covers their ears, withdraws to a corner, goes non-verbal, or becomes rigid and unresponsive. This is not a meltdown (though it can precede one) — it is a shutdown: the nervous system's circuit-breaker tripping.

**Why it happens.** Many children in the target population have atypical sensory processing. Stimuli that are background noise to a neurotypical person (the hum of a refrigerator, the texture of a shirt tag, a sibling laughing) can be experienced as physically painful or overwhelmingly intense. Shutdown is a protective response — the brain reducing input by reducing output.

**Current caregiver burden.** A child in shutdown looks "fine" to an uninformed observer — they are quiet and still. But they are unreachable, and any attempt to prompt, redirect, or engage them can deepen the shutdown or trigger a meltdown. Caregivers must learn to recognize the difference between a calm child and a shut-down child, and must resist the urge to "help" when helping will make things worse.

**HelperWatch response strategy.**

- **Shutdown detection.** The system identifies potential shutdown states through a combination of:
  - Sudden cessation of speech (child was talking, then silence)
  - Heart rate dropping or flattening (parasympathetic withdrawal)
  - Accelerometer showing rigid stillness (not relaxed stillness — no micro-movements)
  - Lack of response to prompts (cue delivered, no verbal or movement response within expected window)
- **Prompt suppression.** When shutdown is suspected, the system immediately reduces its own output. It does not continue issuing routine prompts. A voice in the ear of a child in sensory overload is additional sensory input — the opposite of what is needed.
- **Haptic-only mode.** If the caregiver has configured it, the system can shift to a slow, rhythmic haptic pulse — a gentle sensory anchor that is less intrusive than audio. Or it can go fully silent.
- **Environment change suggestion.** The system notifies the caregiver: *"[Child] may be in sensory overload — they've been non-responsive for [X] minutes. A quieter environment might help."* It does not tell the child to move — it tells the caregiver that the child may need help moving.
- **Gradual re-engagement.** When biometrics and movement suggest the child is coming out of shutdown, the system re-engages minimally and slowly: a gentle tone, a single soft word, or a haptic buzz to signal presence before resuming any verbal cues.

**Feature requirements:**
- Shutdown detection heuristics (silence + stillness + non-responsiveness)
- Automatic prompt suppression during suspected shutdown
- Configurable shutdown response mode (haptic-only, silence, caregiver-defined)
- Caregiver notification for suspected sensory overload
- Gradual re-engagement protocol (minimal-first, escalating slowly)
- Distinguishing shutdown from calm/disengaged states (a hard problem — may require caregiver confirmation)

---

### US-9: Sibling Conflict and Household Chaos

**The scenario.** There are other children in the house. The target child hits a sibling. The sibling screams. The caregiver is in another room. The watch microphone picks up all of it — screaming, crying, accusations, and the target child's voice in the middle of it.

**Why it matters.** HelperWatch is designed to serve a child in a household, not a child in isolation. Sibling dynamics, ambient noise, and multi-person conflict are daily realities, and the system must handle them without making things worse.

**Current caregiver burden.** When the caregiver is not in the room, they have no visibility into what is happening until the screaming reaches them. They arrive to a conflict already in progress, with no context for who started what. The target child may be the aggressor, the victim, or an overwhelmed bystander — and the caregiver must assess the situation in real-time with incomplete, emotionally charged information from multiple children.

**HelperWatch response strategy.**

- **Conflict detection.** The system identifies potential conflict states through transcript analysis (shouting, crying, distress words from multiple voices) and biometric spikes (heart rate elevation, erratic movement).
- **Response suppression.** During active multi-voice conflict, the system should **not** deliver routine prompts or behavioral cues to the child. Issuing a calm instruction during a sibling fight is not helpful and may escalate the target child's frustration.
- **Caregiver alert.** Immediate push notification: *"Possible conflict detected — [Child]'s heart rate spiked and there's loud audio in [room]. You may want to check in."* The system provides data, not judgment. It does not say "your child started a fight."
- **Post-conflict re-engagement.** After the household calms, the system can re-engage the target child with a calming or redirecting cue, depending on caregiver configuration.
- **Voice isolation limitations.** The system should be transparent in its documentation that it cannot reliably distinguish the target child's voice from siblings' voices in all conditions. Transcript classification during multi-voice events will have higher error rates, and the system should default to caution (suppress responses, alert caregiver) rather than confidence.

**Feature requirements:**
- Multi-voice / conflict detection (transcript + biometric signals)
- Automatic response suppression during detected conflict
- Caregiver alert with room context
- Post-conflict re-engagement protocol
- Documented voice-isolation limitations and error-mode behavior

---

### US-10: Bedtime Resistance

**The scenario.** It is bedtime. The child does not want it to be bedtime. They leave their room. They ask for water. They ask a question. They leave again. They need the bathroom. They need another hug. They are not tired. This cycle repeats for 45 minutes to 2 hours.

**Why it happens.** Bedtime combines several of the hardest challenges for the target population: a forced transition (from preferred activity to non-preferred), separation anxiety (from the caregiver and from stimulation), sensory processing differences (the bedroom may feel too quiet, too dark, or too still), and executive dysfunction (the inability to self-initiate the wind-down process).

**Current caregiver burden.** By bedtime, the caregiver has been "on" for 14+ hours. Their capacity for patience is at its lowest precisely when the child's demands are at their highest. Bedtime resistance is one of the most commonly cited sources of caregiver burnout and marital stress in special needs families.

**HelperWatch response strategy.**

- **Bedtime macro activation.** The caregiver taps a one-button macro in the app. The system shifts its entire profile: lower energy prompts, softer tone, reduced biometric alert sensitivity (heart rate naturally rises during bedtime resistance — the meltdown threshold should be adjusted upward).
- **Wind-down scaffolding.** The system guides the child through a configurable wind-down routine: *"Let's get your pajamas on." → "Time to brush teeth." → "Pick a book for bed." → "Okay, let's get in bed."*
- **Room-exit detection with boundary-holding.** If the child leaves the bedroom after the bedtime routine is complete, the system detects the room transition and responds: *"It's bedtime — can you head back to your room?"* The tone is calm and warm, not punitive.
- **Request management.** Common bedtime delay tactics (water, bathroom, one more hug) can be handled by the system with caregiver-configured rules:
  - *Water:* Allow once, then: *"You already had water. Time to stay in bed."*
  - *Bathroom:* Always allow (safety/dignity), but: *"Okay, go ahead. Then right back to bed."*
  - *Questions:* Acknowledge briefly, then redirect: *"That's a great question — let's talk about it tomorrow morning. Right now it's time to rest."*
- **Graduated firmness.** The system's tone and response length should shorten over repeated exits, moving from conversational to brief: *"Hey, back to bed, buddy."* → *"Bed."* The caregiver configures how firm and how fast this graduation occurs.
- **Companion mode.** For children whose bedtime resistance is primarily driven by separation anxiety, the system can provide a low-level "companion" presence: gentle ambient sounds, periodic soft check-ins (*"Still here. You're doing great"*), or a slow breathing guide. This gives the child a sense of presence without requiring the caregiver to physically sit in the room.

**Feature requirements:**
- Bedtime macro with profile shift (tone, alert thresholds, prompt style)
- Configurable wind-down routine steps
- Room-exit detection with configurable re-engagement cue
- Bedtime-specific request classification and rule engine
- Graduated response firmness (configurable rate)
- Companion / ambient presence mode for separation anxiety

---

## The "Humoring" Question: Ethics of Sustained Validating Engagement

Several of the user stories above — particularly US-3 (attention-seeking) and US-2 (perseverative questioning) — require the system to do something that, stated plainly, is uncomfortable: spend extended periods enthusiastically validating a child's interests, answering the same question with unfailing patience, and expressing admiration for Lego towers with the conviction of a paid endorser.

This deserves direct confrontation rather than euphemism.

### The tension

HelperWatch is, in part, a machine that provides the illusion of interested, caring attention. The child speaks, and a patient voice responds with warmth and engagement. The child shows their Legos, and the voice says the Legos are cool. The child asks what is for dinner for the 30th time, and the voice answers with the same patience it had the first time.

Is this merciful, or is it duplicitous?

### The case for mercy

The alternative to the system providing this engagement is not "the child receives the same engagement from a human." The alternative is one of:

1. **The caregiver provides it and burns out.** The 90th "wow, cool Legos" of the day is not sustainable for a human being who also has to cook dinner, manage siblings, work, and maintain their own mental health. The caregiver's patience is a finite resource, and when it runs out, the replacement is not silence — it is irritability, snapping, guilt, and a damaged parent-child dynamic.
2. **Nobody provides it and the child escalates.** The child, receiving no engagement, fills the void with the only tool they have: escalating behavior. The household enters a cycle of provocation, conflict, exhaustion, and guilt. The child's emotional needs are still unmet, but now everyone is also angry.
3. **Nobody provides it and the child withdraws.** The child learns that their interests, their questions, and their bids for connection are not worth pursuing. This is the quietest outcome, and arguably the most damaging.

Against these alternatives, a system that provides consistent, warm, patient engagement — even knowing that the warmth is generated rather than felt — is a net reduction in suffering for the child and the household.

### The case for caution

The system must not become a substitute for human relationship. It must not become the *only* source of validation in the child's life. The ethical framework holds if and only if the system is supplementary — filling gaps in attention, not replacing human connection entirely.

This means:

- **The caregiver must still engage.** The system should periodically prompt the caregiver to check in with their child directly. The system is filling gaps, not building walls.
- **Fading applies to engagement, not just routines.** If the child is receiving proactive check-ins every 10 minutes, and the household stabilizes, the interval should gradually increase. The goal is always less system involvement, not more.
- **The caregiver controls the tone.** The caregiver decides what the system says about the Legos. If the caregiver wants the system to say "those are cool!" — that is the caregiver's judgment about what their child needs. The system is executing a care strategy, not pretending to have feelings.
- **Transparency with the child (when appropriate).** For children who have the cognitive capacity to understand, the caregiver may choose to explain that the watch is a helper — not a friend. This is the caregiver's decision, not the system's.

### The framing

HelperWatch does not pretend to care. It executes a caregiver-approved strategy for providing consistent, patient, validating engagement to a child who needs more of it than any single human can sustainably provide. The caregiver defines the scripts, sets the thresholds, and retains absolute control. The system is a tool — the same way a weighted blanket provides sensory input that a caregiver's arms cannot provide for 8 hours straight, HelperWatch provides attentional input that a caregiver's patience cannot provide for 14 hours straight.

That is not duplicity. That is an assistive technology doing its job.

---

## Feature Requirements Summary

The following table maps each user story to the system features it requires. Features that already appear in the existing roadmap or documentation are marked **Existing**. Features that are new requirements surfaced by these use cases are marked **New**.

| Feature | Required By | Status |
|---------|------------|--------|
| Transcript echo detection | US-1 | **New** |
| Graduated response escalation | US-1, US-2, US-10 | **New** |
| Repetition detection with time window | US-2 | **New** |
| Per-question caregiver answer registry | US-2 | **New** |
| Answer consistency enforcement | US-2 | **New** |
| Proactive system-initiated engagement cues | US-3 | **New** |
| Configurable check-in interval | US-3 | **New** |
| Contextual follow-up response generation | US-3 | **New** |
| Escalation pre-detection (multi-signal) | US-3, US-7 | Partially existing (meltdown intercept) |
| Post-incident re-engagement protocol | US-3, US-7 | **New** |
| Countdown warning sequences | US-4 | **New** |
| Bridging cues (current → next activity) | US-4 | **New** |
| First-step decomposition prompts | US-4 | **New** |
| Room-aware transition follow-up | US-4, US-5, US-10 | Partially existing (room detection) |
| Resistance detection and de-escalation | US-4, US-7 | **New** |
| Routine pause/resume state | US-5 | **New** |
| Re-anchoring cues on room return | US-5 | **New** |
| Step-level aversion pattern detection | US-5 | **New** |
| Room-aware step advancement | US-6 | Existing (core prompting loop) |
| Step-by-step prompting | US-6 | Existing (core prompting loop) |
| Micro-affirmation cue library | US-6 | Existing (mentioned in roadmap) |
| Time awareness cues | US-6 | **New** |
| Dynamic fading (per-step mastery tracking) | US-6 | Existing (mentioned in roadmap) |
| Stuck/timeout detection | US-6 | **New** |
| Rolling-window biometric trend analysis | US-7 | **New** |
| Multi-signal build-up scoring | US-7 | **New** |
| Peak detection with prompt suppression | US-7, US-8 | **New** |
| Configurable peak/shutdown response mode | US-7, US-8 | **New** |
| Recovery detection and buffer period | US-7 | **New** |
| Meltdown trend logging | US-7 | Partially existing (trend reports) |
| Shutdown detection heuristics | US-8 | **New** |
| Gradual re-engagement protocol | US-8 | **New** |
| Conflict detection (multi-voice + biometric) | US-9 | **New** |
| Automatic response suppression during conflict | US-9 | **New** |
| Bedtime macro with profile shift | US-10 | Partially existing (macro triggers) |
| Bedtime request classification and rules | US-10 | **New** |
| Graduated response firmness | US-10 | **New** |
| Companion / ambient presence mode | US-10 | **New** |
| Caregiver check-in prompts ("engage your child") | Ethics | **New** |

**Summary:** Of the 37 distinct features identified, **6** are already in the existing documentation or roadmap, **5** are partially covered, and **26** are new requirements surfaced by these behavioral use cases.

---

## Related Documents

- [System Architecture](System%20Architecture.md) — Component interactions and data flows
- [Cloud Backend](Cloud%20Backend.md) — AI orchestration and routine engine
- [Caregiver Mobile App](Caregiver%20Mobile%20App.md) — Macro triggers, intercom, and trend reports
- [Wearable App](Wearable%20App.md) — Watch-side cue playback and biometric sensing
- [Safety and Ethics Overview](../ethics/Safety%20and%20Ethics%20Overview.md) — Ethical framework
- [Risks and Mitigations](../ethics/Risks%20and%20Mitigations.md) — Risk register
- [Adaptive Response Architecture](Adaptive%20Response%20Architecture.md) — How the system decides what to do: profiles, protocols, and context evaluation
- [Mission and Values](../project/Mission%20and%20Values.md) — Core principles
