# Operationalisation: Ecosystem Taxonomy → Sensors → Cousin Confirmation → Attack Story Chaining

This repository is not a pile of isolated KQL rules.

It is a **production detection architecture** built on one principle:

> **Composite rules are sensors. Incidents are narratives.**

We do not build monolithic “kill-chain mega rules.”  
We deploy **Minimum Truth composites** as clean, high-fidelity sensors — then chain them using an ecosystem model.

This document operationalises:

- **Cousin ecosystems** (capability parity across adjacent surfaces)
- **Attack-type ecosystems** (incident narratives across stages)
- **Telemetry substrate ecosystems** (engineering reality: baselines/cost/reliability)

It also defines:

- the **required output contract**
- how to chain into **alerts + incidents**
- how to operationalise **HunterDirectives** as embedded runbooks

---

## The 3 Ecosystem Types

Threat detection becomes messy only when ecosystems are undefined.  
This framework enforces **three ecosystem layers**.

---

## 1) Cousin Ecosystems (Capability Parity)

**Definition:**  
Rules that share the same attacker intent, but live on adjacent execution surfaces.

> Cousins cover the same goal through different truth anchors.

They are **paired sensors**, not merged logic.

### Why Cousins Exist
- Preserve fidelity
- Prevent noise dilution
- Ensure full surface coverage

### Example: Persistence — Scheduled Tasks

| Cousin Rule | Truth Anchor | Telemetry Surface |
|------------|-------------|------------------|
| `ScheduledTask_Abuse_RUNDLL_AWARE_Classifier` | `schtasks.exe /create` or `/change` | Process |
| `Registry_Persistence_Background_Service_TaskCache` | TaskCache persistence materialisation | Registry |

Same attacker goal. Different noise domain.  
**Separate rules. Same cousin family.**

---

## 2) Attack-Type Ecosystems (Incident Narratives)

**Definition:**  
Multi-stage attacker campaigns spanning multiple surfaces:

Ingress → Execution → Persistence → Lateral → C2

Attack-type ecosystems are **not rules**.

They are the **incident correlation grammar** that stitches truths together.

### Why Attack-Type Ecosystems Exist
- Build intrusion narratives safely
- Escalate when multiple truths converge
- Avoid ghost-chain joins inside single queries

### Example Chain (Narrative)
- Ingress Tool Transfer truth  
- + Proxy Execution truth  
- + Persistence cousin truth  
→ Intrusion story confirmed

---

## 3) Telemetry Substrate Ecosystems (Engineering Reality)

**Definition:**  
Grouping by primary telemetry substrate:

- Process ecosystem  
- Registry ecosystem  
- Identity ecosystem  
- Network ecosystem  
- Cloud control-plane ecosystem  

### Why Telemetry Substrate Ecosystems Exist
This determines:

- baseline strategy
- cost + scheduling
- reliability guarantees
- noise domains
- which rules can share prevalence models

Without this layer, detection systems become unmaintainable.

---

# Production Rule Contract (Non-Negotiable Output Schema)

Every composite rule must emit a normalized output shape.  
This is what makes correlation **trivial, stable, and production-safe**.

## Required Fields

Each hunt must output:

- `RuleId`
- `RuleName`
- `TelemetryEcosystem`
- `CousinFamily`
- `AttackStages` (array)
- `TruthAnchor`
- `RiskScore`
- `RiskLevel`
- `EvidenceKey` (fingerprint/dedupe)
- `HunterDirectiveSummary`
- `HunterDirectivePack` (full guidance / playbook)

---

## Example: Standard Output Shape

```kql
| extend
    RuleId = "PERSIST_05_TASKCACHE",
    RuleName = "Registry_Persistence_Background_Service_TaskCache",
    TelemetryEcosystem = "Registry",
    CousinFamily = "Persistence.ScheduledTask",
    AttackStages = dynamic(["Persistence"]),
    TruthAnchor = "RegistryValueSet under TaskCache/Services persistence surfaces",
    EvidenceKey = hash_sha256(strcat(DeviceId, "|", tostring(RegistryKey), "|", tostring(RegistryValueData)))
```

## Correlation Model (Two Layers Only)

This framework correlates at exactly **two layers** — never monolithic kill-chain rules.

---

### Layer A — Cousin Confirmation (Capability Reinforcement)

Cousin correlation increases confidence **inside the same capability family**.

#### Example

If we observe:

- TaskCache silent persistence (**Registry cousin**)  
AND within 24 hours:  
- `schtasks.exe` creation/modification activity (**Process cousin**)

Then we escalate:

> **Persistence capability confirmed at high confidence**

This is **not** a monolith.  
It is **cousin reinforcement**.

---

### Layer B — Attack-Type Story Chaining (Incident Escalation)

Attack-type correlation is where **incidents are created**.

#### Example

If we observe within 6–24 hours:

- Ingress truth  
- Execution truth  
- Persistence truth  

Then:

> **Intrusion chain likely → Create Incident**

This is how Tier-1 sensors become Tier-2 incidents.

---

# Sentinel / Engineering Deployment Model

The production deployment pattern is:

---

## Step 1 — Deploy Sensors (Composite Rules)

All composites run scheduled.

They output normalized detection rows:

- Minimum Truth  
- Reinforcement  
- Risk scoring  

These are always-on truths.

---

## Step 2 — Write to a Unified Detection Stream

All outputs land in one logical stream (e.g.):

- `CompositeDetections_CL` (custom table), **or**
- Sentinel analytics output/workspace table pattern, **or**
- MDE Advanced Hunting export pipeline

This becomes the detection backbone.

---

## Step 3 — Run Only Two Correlation Rules

### Correlation Rule 1: `CousinConfirm`

- groups by `CousinFamily`
- escalates confidence if multiple cousins fire

### Correlation Rule 2: `AttackStoryChain`

- groups by `AttackStages`
- creates incidents when multiple stages converge

No monolithic kill-chain queries required.

---

# HunterDirectives (Operationalised Correctly)

HunterDirectives are not “extra text.”

They are embedded runbooks.

This framework enforces three directive layers:

---

## 1) `HunterDirectiveSummary` (Alert-Safe)

Short, immediate meaning:

- why this fired  
- what matters  
- first pivot  

Keep it short (alert-friendly).

---

## 2) `HunterDirectivePack` (Case Enrichment)

Full SOC guidance as `pack_array(...)`:

- pivots  
- scoping steps  
- escalation conditions  
- suppression notes  

---

## 3) Cousin Routing Hints (Machine-Usable)

Each rule provides recommended cousin pivots:

```kql
| extend NextBestCousins = dynamic([
  "Persistence.ScheduledTask.SchtasksClassifier",
  "Persistence.ScheduledTask.TaskCache"
])
```
This enables automation and consistent analyst routing.

## Golden Operational Model

This framework is deployable because:
- Truth defines the sensor
- Reinforcement increases confidence
- Cousins provide surface parity
- Attack ecosystems create incidents
- Telemetry ecosystems keep it scalable
- Correlation happens outside rules
- HunterDirectives become embedded playbooks

## Core Deployment Rule
- Composite rules detect truths.
- Cousins confirm capabilities.
- Attack ecosystems build incidents.
- Telemetry ecosystems keep it scalable.

 **This is Minimum Truth detection engineering in production form**    .


