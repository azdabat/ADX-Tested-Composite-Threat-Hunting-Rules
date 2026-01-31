# Cousin_Discovery_Log.md  
**Composite Detection Framework — Cousin Ecosystem Discovery Journal**  
Author: **Ala Dabat**  
Status: **Active / Living Document**  
Purpose: Track empirically validated cousin ecosystems discovered through **ADX-Docker testing**, **Empire telemetry**, and **realistic attack-chain simulation**.

---

## Why This File Exists

Cousin ecosystems are **not theoretical**.  
They are not chosen from MITRE menus.

They are discovered through:

- adversary emulation (Empire, Sliver, Atomic Red Team)
- telemetry convergence
- attack-surface reinforcement patterns

This log documents:

- which composites fired
- what other truths consistently co-occurred
- which cousins are now operationally validated

> **Cousins evolve over time.**  
> This file is the empirical backbone of ecosystem correlation.

---

# Cousin Model Reminder

A **Cousin Rule** is:

- a sibling detection surface
- inside the same attacker capability family
- confirming the same intent through a different telemetry anchor

**Cousins are reinforcement — not monolithic kill-chains.**

---

# Discovery Log Format

Each entry records:

- **Primary Sensor Rule**
- **Confirmed Cousin Surface**
- **Attack Capability**
- **Telemetry Proof**
- **Operational Outcome**

---

# ✅ Confirmed Cousins (So Far)

---

## 1. Scheduled Task Persistence Ecosystem

### Primary Sensor  
**Scheduled_Tasks_Rundll_Integration.kql**  
Truth Anchor: `schtasks.exe /create` or `/change`

### Confirmed Cousin  
**Registry_Persistence_Background_Service_TaskCache_v2.kql**  
Truth Anchor: TaskCache registry materialization (`Tree/Tasks`)

### Capability Confirmed  
**Scheduled Task Persistence (Silent + CLI)**

### Telemetry Proof  
Empire + ADX harness consistently shows:

- schtasks activity
- followed by TaskCache writes
- persistence exists even when schtasks is bypassed

### Operational Use  
If both cousins fire within 6–24h:

> Persistence capability confirmed at HIGH confidence

---

## 2. Rundll32 Proxy Execution Ecosystem

### Primary Sensor  
Scheduled Task Abuse Rule (Rundll-aware)

### Confirmed Cousin Surface  
Rundll32 Execution Truth Models:

- Script protocol handler abuse (`javascript:` / `mshtml`)
- Remote UNC/WebDAV load
- INF ProxyExec (`advpack.dll LaunchINFSection`)

### Capability Confirmed  
**Signed Binary Proxy Execution (T1218.011)**

### Operational Use  
Scheduled task firing with Rundll32 category escalates:

- HIGH → CRITICAL severity
- immediate pivot into execution hunts

---

## 3. Registry Persistence vs Service Persistence Cousins

### Primary Sensor  
Registry TaskCache anchor

### Confirmed Cousin  
Service ImagePath persistence:

- `HKLM\SYSTEM\Services\*\ImagePath`

### Capability Confirmed  
**Background Persistence via Service Registration**

### Telemetry Proof  
ADX stress harness shows:

- ImagePath writes converge with LOLBin primitives
- often paired with writable path staging

### Operational Outcome  
Service persistence becomes cousin reinforcement for:

- Task persistence
- lateral persistence staging

---

## 4. TaskCache Silent Persistence Cousin Pair

### Primary Sensor  
Registry TaskCache writes

### Cousin Surface  
CLI Scheduled Task creation (`schtasks.exe`)

### Capability Confirmed  
Attackers register tasks via:

- COM/API (silent)
- schtasks.exe (explicit)

### Operational Rule  
Deploy both cousins:

- Registry = stealth truth
- Process = noisy but contextual truth

Together = confirmed persistence capability.

---

## 5. PowerShell Persistence Cousins

### Primary Sensor  
Scheduled Task action uses PowerShell

Examples:

- `powershell -enc ...`
- `IEX(New-Object Net.WebClient)...`

### Cousin Surface  
Registry Run key persistence:

- `HKLM\...\Run`

### Capability Confirmed  
**Script-based persistence + staging**

### Operational Outcome  
PowerShell task + RunKey write = multi-surface persistence intent.

---

## 6. Ingress → Persistence Cousin Discovery

### Primary Sensor  
Ingress Tool Transfer rules (staging drops)

### Cousin Surface  
Persistence activation surfaces:

- TaskCache
- Scheduled Tasks
- Services

### Capability Confirmed  
Attackers rarely stop at ingress.

Ingress truth becomes cousin precursor for:

- persistence truth within 24h

---

## 7. WMI Fileless Persistence Cousins

### Primary Sensor  
WMI Permanent Subscription rules

Truth Anchor:

- EventFilter + Consumer creation

### Confirmed Cousins  
Adjacent persistence surfaces:

- Scheduled Tasks
- Registry Run keys

### Capability Confirmed  
**Fileless persistence cluster**

### Operational Note  
WMI is often paired with:

- task fallback persistence
- registry backup persistence

---

## 8. Credential Access Cousin Cluster

### Primary Sensor  
LSASS Dump Composite

Truth Anchor:

- LSASS access/dump behavior

### Cousin Surface  
NTDS / SAM extraction rules

Truth Anchor:

- Hive interaction
- Domain credential dumping artifacts

### Capability Confirmed  
Credential access ecosystem convergence:

- workstation dump → domain dump progression

---

## 9. Lateral Movement Cousins (SMB Ecosystem)

### Primary Sensor  
SMB Service Execution rules

Truth Anchor:

- Remote service creation / execution

### Confirmed Cousins  
Persistence follow-ons:

- Service ImagePath writes
- Scheduled task registration

### Capability Confirmed  
**Remote execution → persistence establishment**

---

## 10. OAuth Identity Abuse Cousins

### Primary Sensor  
OAuth Consent Abuse rule

Truth Anchor:

- High-risk scope grant

### Confirmed Cousin  
Token Replay Anomaly rule

Truth Anchor:

- ASN + UA divergence

### Capability Confirmed  
Cloud persistence + session theft cluster

---

# ✅ Current Cousin Families (Operational Index)

| Cousin Family | Primary Truth Rule | Confirmed Cousins |
|-------------|-------------------|------------------|
| ScheduledTask.Persistence | schtasks.exe create/change | TaskCache registry materialization |
| Registry.Persistence | TaskCache + Run keys | Service ImagePath persistence |
| ProxyExecution.Rundll32 | Rundll categories in /tr | Direct Rundll32 execution hunts |
| ScriptPersistence.PowerShell | Task action PowerShell | Run key + encoded registry payloads |
| Fileless.Persistence.WMI | Permanent subscription | Task fallback + Run fallback |
| CredentialAccess.Cluster | LSASS dump | NTDS/SAM dump cousins |
| LateralMovement.SMB | Remote service exec | Service persistence + task persistence |
| Identity.CloudAbuse | OAuth consent | Token replay anomalies |

---

# Golden Operational Rule

Composite rules detect truths.  
Cousins confirm capabilities.  
Attack ecosystems create incidents.  
Telemetry ecosystems keep it scalable.

---

# Next Cousin Candidates (Pending Validation)

These are suspected but not yet confirmed through repeated telemetry:

- WinRM remote execution cousin (T1021.004)
- DCOM remote exec cousin (T1021.003)
- Silent WMI remote process creation cousin
- Archive staging → exfil cousin convergence

---

# How To Update This Log

Every time testing reveals convergence:

1. Record the primary rule that fired  
2. Record the sibling surface that followed  
3. Confirm repeated co-occurrence  
4. Promote to operational cousin status  
5. Add routing hints into HunterDirectives

---

# Framework Outcome

This document proves:

- Cousins are empirical
- Ecosystem coverage is systematic
- Correlation remains outside monolithic rules
- Your framework is production-realistic

---

**Status:** Battle-tested cousins validated through ADX + Empire simulation.  
**Next Step:** Correlation layer operationalisation in Sentinel (CousinConfirm + AttackStoryChain).
