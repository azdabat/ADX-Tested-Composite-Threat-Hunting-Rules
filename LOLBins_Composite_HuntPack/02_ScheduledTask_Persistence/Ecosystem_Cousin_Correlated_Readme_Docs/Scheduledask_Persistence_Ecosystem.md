# Scheduled Task Persistence Ecosystem (Rundll32-Aware + Silent TaskCache Cousins)

This folder contains a **cousin-based persistence ecosystem** for detecting attacker abuse of **Scheduled Tasks** in Windows environments.

Rather than building one monolithic “persistence mega-rule”, this ecosystem splits detection into two **paired composite sensors**:

- **Process Truth (CLI Task Creation)**
- **Registry Truth (Silent TaskCache Persistence)**

Together, these rules provide full coverage of how modern attackers establish task-based persistence.

---

## Why This Ecosystem Exists

Scheduled Tasks are one of the most abused persistence mechanisms because they allow:

- execution at logon/startup
- SYSTEM-level persistence
- stealth via legitimate Windows tooling
- indirect execution via LOLBins like `rundll32.exe`

Attackers register tasks in **two primary ways**:

| Method | Example | Visibility |
|-------|---------|------------|
| **CLI Creation** | `schtasks.exe /create` | Process telemetry |
| **Silent API/COM Registration** | TaskCache registry writes | Registry telemetry |

Most SOCs only detect the first.

This ecosystem detects **both**.

---

# Rule 1 — Scheduled Task Abuse (Creation/Modification) — RUNDLL-AWARE

## File

`Scheduled_Tasks_Rundll_Integration.kql`

## Minimum Truth Anchor

A scheduled task is created or modified via:

- `schtasks.exe /create`
- `schtasks.exe /change`

This is the baseline truth that task persistence is being attempted.

---

## Reinforcement (Attacker Tradecraft Inside `/tr`)

This rule becomes high-fidelity when the task action contains dangerous primitives such as:

### Rundll32 Script/Protocol Handler Abuse

```cmd
schtasks /create /tn Maintain /tr "rundll32 javascript:...RunHTMLApplication..."
```


