---

---

# ✅ README #2 — Production-READY Composite Rules (Deployed Layer)

```markdown
# Production-READY Composite Threat Hunting Rules

**Author:** Ala Dabat  
**Platform:** Microsoft Sentinel + Microsoft Defender for Endpoint  
**Status:** Deployment-Grade Composite Detections

---

## Purpose of This Folder

This repository contains **production-hardened composite detections**.

These rules are:

- tuned against enterprise noise  
- validated through telemetry baselining  
- reinforced with prevalence scoring  
- structured for SOC operational deployment  
- aligned to MITRE ATT&CK  
- delivered with analyst-facing directives  

This is the **deployable layer**.

---

## Composite Detection Framework (Core Methodology)

All rules follow the same architecture:

---

## 1. Minimum Truth Anchor

Every detection begins with a behavioural truth that is unavoidable:

- svchost Schedule spawning PowerShell  
- TaskCache registry write  
- Named pipe deviation  
- Shadow copy deletion  
- Service stop wave  

Truth anchors prevent brittle IOC logic.

---

## 2. Reinforcement Layer (Convergence)

Confidence is raised only when signals converge:

- execution + inbound SMB  
- pipe anomaly + service creation  
- encryption burst + recovery deletion  
- rare LOLBin + persistence artifact  

Reinforcement strengthens truth — it does not redefine it.

---

## 3. Noise Suppression (Enterprise Reality)

Rules are hardened against:

- updater activity  
- backup workflows  
- admin tooling  
- common service operations  
- benign prevalence patterns  

Noise is controlled structurally.

---

## 4. Org Prevalence Scoring

Every composite incorporates rarity weighting:

- rare behaviour = higher risk  
- widespread behaviour = likely benign  

This prevents alert fatigue at scale.

---

## 5. Confidence + Severity Output

Rules output:

- RiskScore  
- Severity tier  
- Confidence explanation  
- Analyst directives  

Scoring is used as a triage multiplier, not a trigger gimmick.

---

## Tier Structure in Production

---

### Tier-1 — Enterprise Backbone Composites

Tier-1 rules represent baseline organisational must-haves:

- SMB/service lateral movement  
- scheduled task execution surfaces  
- credential access anchors  
- ransomware impact backbone  
- core persistence truths  

Tier-1 is the minimum viable behavioural detection coverage.

---

### Tier-2 — Advanced Composite Correlation

Tier-2 rules extend Tier-1 with:

- multi-table correlation  
- rarity + spread penalties  
- convergence scoring engines  
- higher fidelity suppression logic  
- IR-ready enrichment  

Tier-2 is designed for L2/L3 threat hunting teams.

---

### Tier-3 — Threat Campaign Ecosystems (Adjacent Layer)

Tier-3 rules (SilverFox, EtherRAT, React2Shell) remain adjacent because:

- they are threat-specific  
- noise domains evolve rapidly  
- deployment depends on active risk context  

Tier-3 is not baseline — it is escalation coverage.

---

## Rule Maturity Lifecycle (Operational Standard)

A rule is promoted here only after:

1. Telemetry validated  
2. Tenant baseline measured  
3. Synthetic + real scenario testing  
4. Prevalence scoring implemented  
5. Noise gates enforced  
6. Analyst workflow embedded  

This ensures every rule here is truly production-grade.

---

## SOC Usage

These rules are designed to support:

- proactive hunting  
- detection-as-code CI/CD pipelines  
- incident correlation  
- automated triage enrichment  
- operational resilience  

Each detection is both:

- a behavioural sensor  
- an investigation guide

---

## Core Philosophy

Most organisations fail because they deploy:

- shallow IOC spam  
- noisy anomaly alerts  
- unscored detections  
- brittle signatures  

This repository exists to deliver:

> Behavioural truth-based detection systems that scale.

---

**Production-ready means: tested, tuned, reinforced, deployable.**
