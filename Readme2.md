# Threat Hunting Philosophy & Detection Architecture (Minimum Truth Framework)

**Author:** Ala Dabat  
**Platform Focus:** Microsoft Sentinel + Microsoft Defender for Endpoint (MDE)  
**Audience:** L2/L2.5 Threat Hunters • Detection Engineers • Security Leads  

---

## Why This Exists

Most SOCs don’t fail because they lack tools. They fail because detections are:

- over-correlated “kill chain mega rules”
- noisy, speculative, or assumption-driven
- not written for the operational reality of enterprise environments

This repository documents a practical detection architecture that:

- starts from **attack truth**
- uses correlation only when it is structurally required
- uses **reinforcement** (not dependency) to control noise
- outputs SOC-ready directives (what it means + what to do)

---

## Core Principle (TL;DR)

> **Start with the Minimum Truth required for the attack to exist.**  
> Everything else is reinforcement — not dependency.

If the Minimum Truth is not met, the “attack” is not real.

---

## Detection Maturity Model

### 1) Minimum Truth (Baseline Anchor)
Each rule is anchored on one unavoidable attacker action.

Examples:
- LSASS theft → LSASS must be accessed
- Kerberoasting → service tickets must be requested
- OAuth abuse → high-risk scope grant must occur
- Named pipe C2 → anomalous pipe creation/connection must exist

### 2) Composite Hunts (Default for Enterprise)
Most effective hunts are composites that:
- group related high-signal indicators
- stay within a coherent mechanism/noise domain
- join only when unavoidable

### 3) Reinforcement (Confidence, Not Dependency)
Reinforcement improves confidence via:
- parent/child context
- command-line primitives
- writable paths
- network proximity
- org prevalence (rarity)

Reinforcement must never redefine the truth anchor.

### 4) Behavioural Chains (Used Sparingly)
Correlation inside one rule is mandatory only when a technique cannot be confirmed from a single event stream.

Example: DLL sideloading  
- DLL drop alone is benign  
- DLL load alone is benign  
- the attack truth exists only when both converge

---

## Truth vs Reinforcement vs Cousins vs Incidents

**Mental Model**
1) **Truth Anchor = Sensor**  
2) **Reinforcement = Evidence**  
3) **Cousins = Adjacent sensors (same intent, different surface/noise domain)**  
4) **Incident = Narrative stitching (multiple sensors form the story)**  

---

## The Four Rules of Detection Architecture

### Rule 1 — Split only when the **Truth Anchor Mechanism** changes
Split a composite when the non-negotiable truth changes in mechanism.

Examples of real truth-anchor changes:
- `services.exe` execution truth → `svchost(schedule)` execution truth  
- SMB lateral truth → WMI/DCOM lateral truth  
- endpoint execution truth → identity sign-in truth  
- named pipe C2 truth → HTTP beaconing truth  

These are different sensors and should be separate rules.

### Rule 2 — Do NOT split when reinforcement crosses tables
Reinforcement may use registry/file/network/process telemetry **as long as it remains optional** and does not replace the baseline truth.

Example:
- Truth = `svchost(schedule)` spawns suspicious child after SMB
- Reinforcement = TaskCache registry artifacts / task XML drops / org prevalence

Truth remains execution. Registry is supporting evidence.

### Rule 3 — Split when the **noise domain** changes
If the same intent lives in a different operational noise environment (different allowlists, different baseline), split.

Example:
- `services.exe` spawn (quiet) vs `svchost(schedule)` spawn (noisy)  
Same attacker intent (remote exec), different noise domain → cousin rules.

### Rule 4 — Keep composite when you are refining context
Scoring, enrichment, classification, and reinforcement belong inside the rule when truth stays the same.

---

## Why Overlap Is Valid (and sometimes required)

Overlap is not redundancy when the **truth differs**.

Example:
- `Registry_Persistence_Background_Service_TaskCache`  
  - Truth: TaskCache / Services registry persistence exists  
  - This can fire even if the task never executes

- `SMB_TaskExec_Svchost_Schedule_Empire_Cousin`  
  - Truth: victim-side scheduled-task execution after SMB exists  
  - This can fire even if registry visibility is degraded

When both fire together, that’s **incident-level convergence**.

> Rules are sensors. Incidents stitch sensors into attack stories.

---

## Organisational Prevalence (Used Correctly)

Prevalence is not a detection trigger.  
It is a reinforcement / prioritisation amplifier applied **after truth exists**.

- Low prevalence (1–2 hosts) → targeted intrusion likelihood ↑
- High prevalence → tooling/admin likelihood ↑

**Prevalence decides urgency, not existence.**

---

## Correlation vs “Ghost Chains”

A “Ghost Chain” is when a rule stitches unrelated background activity into a fake kill chain.

Correlation adds dependency:
- missing telemetry breaks detection
- arbitrary windows create false narratives

Use correlation inside a rule only when:
- the technique cannot exist without convergence
- the join reduces ambiguity (not inflates complexity)

Otherwise:
- build clean sensors
- correlate at the incident level

---

## Rule Template (Minimum Truth → Reinforcement → Confidence)

Every composite should be explainable quickly:

1) Minimum Truth (one anchor)
2) Reinforcement (2–4 signals max)
3) Noise suppression (explicit)
4) Org prevalence (optional scoring)
5) SOC-ready directives (what it means + pivots + containment)

> If a hunt cannot be explained in 60 seconds, it is too complex.

---
