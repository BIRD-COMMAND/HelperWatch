### **Project Brief: The SmartScaffold / HelperWatch Initiative**

This brief establishes the mission, ethical constraints, and technical philosophy for a cognitive scaffolding system designed to empower severely impacted special needs children and their caregivers through accessible, low-cost technology.

### **Executive Overview**

There is a real and meaningful opportunity to transform and improve the daily lives of special needs families by providing a functional system that can help share the responsibility of this specialized, intensive caregiving. The project aims to create such a "cognitive prosthetic" that provides real-time, location-aware verbal cues and behavioral monitoring for children requiring near-constant executive function support. By leveraging matured, affordable consumer electronics and secure cloud AI processing, we are building a system that bridges the gap between total human-dependent care and maximized functional independence—all while maintaining absolute data security and safety for the family.

### **Core Mission & Values**

* **Empowerment through Independence:** To provide children with the prompts and emotional support needed to navigate daily routines with reduced human intervention.  
* **Caregiver Relief:** To offload the repetitive, mechanical task of constant prompting, allowing parents to move from "taskmaster" to a purely supportive role.  
* **Radical Accessibility:** To ensure the system is financially and technically reachable for any family by utilizing repurposed hardware and a simple cloud-based backend.  
* **Ethical Safety by Design:** To treat privacy not as a feature, but as a hard-coded requirement through encrypted cloud STT with immediate audio deletion, strict data minimization, and transparent educator-led risk mitigation.

### **Key Functional Capabilities**

| Feature | Impact for the Child | Impact for the Caregiver |
| ----- | ----- | ----- |
| **Room-Aware Scaffolding** | Receives step-by-step instructions relevant to their goal and location (e.g., "pick up your toothbrush" when in the bathroom). | Reduced need for physical supervision and repetitive verbal commands. |
| **Biometric Meltdown Intercept** | Receives calming haptic or audio cues when heart rate or pacing indicates rising distress. | Early warning alerts via mobile app before a full meltdown occurs. |
| **Micro-Affirmations** | Constant positive reinforcement for small successes, improving self-esteem. | Shifts the household dynamic from constant correction to positive reinforcement. |
| **Intercom System Co-Regulation Tool** | Parental instructions are integrated seamlessly into the familiar AI voice and guidance. | Ability to provide "remote coaching" or arbitrary notifications without physically interrupting the child's focus. |

### 

### **Technical Philosophy (The "Accessible-First" Stack)**

In alignment with our goal to avoid custom electrical engineering and expensive proprietary hardware, the project adheres to the following technical pillars:

* **Commodity Hardware:** The system primarily targets older, refurbished Android wearables. These provide pre-certified microphones, speakers, and sensors at a fraction of the cost of custom builds.  
* **Indoor Mesh Tracking:** Instead of unreliable indoor GPS, the system relies on low-cost ESP32 boards acting as room scanner nodes to achieve \> 90% room-level accuracy in position / context / activity-tracking.  
* **Zero-Config Cloud Software:** The backend is hosted as a secure Cloud Backend, eliminating the setup and maintenance complexity of local servers. AI orchestration (including LLMs) and speech-to-text transcription run in the cloud with deterministic script guardrails, encrypted transit, and immediate audio deletion.

### **Ethical Guardrails & Risk Mitigations**

Because this system involves constant monitoring, the following "hard lines" are non-negotiable:

* **Data Sovereignty & Minimization:** To neutralize surveillance risks, the watch streams encrypted audio to the Cloud Backend for transcription. Raw audio is processed transiently in memory and deleted immediately after transcription — never stored at rest. All cloud processing is transient, and no data is stored permanently on our servers unless explicitly opted into by the caregiver.
* **Audio Privacy:** Audio is streamed encrypted (TLS) from the watch to the Cloud Backend, transcribed in memory, and deleted immediately. No audio is ever stored at rest on any device or server.
* **Deterministic Guardrails:** The AI acts as a router for parent-approved scripts rather than having full creative freedom, preventing unsafe "hallucinations".  
* **Bystander Privacy:** Transcripts are processed transiently in-memory and deleted immediately, and "Privacy Zones" must be easy for caregivers to toggle.  
* **Dynamic Fading:** The system must be designed to reduce its own prompting frequency as the child demonstrates mastery, preventing long-term dependency.

### **Future Development Direction**

Moving forward, we will focus on refining the mobile app experience for caregivers, developing the ESP32 room scanner firmware, and building out the secure Cloud Backend. This ensures that the focus remains on the behavioral impacts and benefits of the system rather than the complexity of home server setup.