# L3 Sensitive Data Recon — Secrets Discovery (Composite Hunt)

**Rule:** `L3_Sensitive_Data_Recon_Hunt_V4`  
**Author:** Ala Dabat  
**Status:** Production-Ready (Risk-Based Composite)

---

## Purpose

Detect attacker-like filesystem reconnaissance for:

- Password material  
- Credential files  
- Cloud tokens  
- SSH/private keys  
- Unattended installation secrets (`unattend.xml`)  

This is a **Composite L3 hunt** designed to surface *intent-driven secrets discovery* while suppressing common admin noise.

---

## Framework Alignment (Minimum Truth → Reinforcement → Scoring)

### Minimum Truth (Baseline Anchor)

A filesystem search primitive executes **AND** the command line contains credential/secrets intent.

Examples:

- `findstr password *.txt`
- `Select-String "aws_secret_access_key"`
- `dir /s id_rsa`

---

### Reinforcement Signals (Confidence Boosters)

- Content inspection (`findstr`, `Select-String`)
- High-value targets (`unattend.xml`, `.env`, `id_rsa`, AWS/Azure tokens)
- Recursive deep sweeps (`/s`, `-recurse`)
- Userland targeting (`\Users\`, `\AppData\`)
- Suspicious parent processes (Office, script engines)

---

### Noise Suppression

System/admin-heavy paths reduce score unless paired with strong secret intent:

- `C:\Windows\System32\`
- `Program Files\`
- Log directories

---

## Severity Model

| Severity   | Meaning |
|-----------|---------|
| CRITICAL  | Targeted secret discovery + deep content search |
| HIGH      | High-value secret targeting or recursive user sweep |
| MEDIUM    | Sensitive keyword recon requiring context validation |

---

## SOC Output Includes

- RiskScore (explainable)
- DetectionSummary
- Inline HunterDirective
- PivotPack (next investigative actions)

---

## Analyst Pivots

**Immediate follow-ups:**

1. Process ancestry (Office/script → recon tool)
2. Post-recon staging (7z/rar/zip, exfil tooling)
3. Secret rotation if cloud tokens or `.env` files are targeted
4. Credential access follow-on (LSASS/NTDS/Kerberos anomalies)

---

## MITRE ATT&CK

- **T1552** — Unsecured Credentials  
- **T1083** — File and Directory Discovery  

---

## Notes

This hunt is designed as a **risk amplifier** for early-stage compromise discovery.  
For higher maturity environments, add **Org Prevalence scoring** on rare commandline patterns as an optional reinforcement layer.

---
