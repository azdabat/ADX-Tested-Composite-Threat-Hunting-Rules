# Threat Hunting Philosophy & Detection Design

**Author:** Ala Dabat  
**Focus:** Practical, adversary-informed threat hunting for real SOC environments  
**Audience:** L2 / L2.5 Threat Hunters, Detection Engineers, Security Leads  

---

## Why This Repository Exists

Most SOCs fail at threat hunting not because they lack tools, but because:

- Detections are **over-engineered**
- Behavioural chains are **forced where they are not required**
- Analysts are flooded with **noise disguised as intelligence**
- Rules are written without regard for **operational reality**

This repository documents a **deliberate, practical methodology** for building threat hunts that:

- Scale to real SOC teams
- Preserve signal fidelity
- Reduce analyst fatigue
- Use behavioural logic **only when it is genuinely required**

---

## Core Philosophy (TL;DR)

> **Start with the minimum truth required for the attack to exist.** > Everything else is reinforcement — not dependency.

---

## The Detection Maturity Model I Use

### 1. Reductive Baseline (Truth First)
Every attack has a **minimum condition that must be true**.  
If this condition is not met, the attack is **not real**.
* *Example:* LSASS credential access → *LSASS must be accessed.*

### 2. Composite L2 Hunts (Default)
Most attacks do not require full behavioural chains. We group **related high-signal indicators** into **composite hunts** that use a single telemetry source where possible. This is where **most threat hunting should live**.

### 3. Reinforcement (Scoring, Context, Rarity)
Once the baseline truth is met, we **reinforce confidence** using parent/child relationships, network proximity, or rarity. Reinforcement **improves fidelity** — it should never be required for detection.

### 4. Behavioural Chains (Used Sparingly)
Behavioural correlation is **only used when an attack cannot exist without it** (e.g., DLL Sideloading).

---

## The Composite Portfolio: Top 10 High-Fidelity Hunts

This repository contains **10 validated Composite Rules** that cover the critical phases of the kill chain. Each rule is designed to be **Production Ready** with low noise.

| Category | Rule Name | MITRE | Why It Matters |
| :--- | :--- | :--- | :--- |
| **Persistence** | **WMI Event Subscription & Consumer** | T1546.003 | Detects fileless persistence via `scrcons.exe` (e.g., Yeabots, Empire). |
| **Defense Evasion** | **LOLBin Chaining & Proxy Execution** | T1218 | Detects `rundll32`, `regsvr32` used to bypass controls. |
| **Exfiltration** | **Archive Staging (Smash & Grab)** | T1560 | Detects rapid collection and compression of data (`7z`, `rar`) before exfil. |
| **Privilege Escalation** | **Account Manipulation (User Add)** | T1098 | Detects unauthorized user creation or group modification. |
| **Cloud Identity** | **OAuth Illicit App Consent** | T1528 | **(Cloud)** Detects malicious applications requesting high-risk scopes. |
| **Credential Access** | **NTDS.dit / Active Directory Dumping** | T1003.003 | **(Critical)** Detects attempts to access the AD database via `vssadmin` or `ntdsutil`. |
| **Lateral Movement** | **SMB / Named Pipe Impersonation** | T1021.002 | Detects incoming 445 traffic spawning command shells (PsExec behavior). |
| **Defense Evasion** | **DLL Sideloading (Image Loads)** | T1574.002 | Detects signed binaries loading unsigned DLLs from writable paths. |
| **Impact** | **Shadow Copy Deletion** | T1490 | **(Ransomware)** Detects precursor activity to encryption. |
| **Credential Access** | **Kerberoasting (Encryption Downgrade)** | T1558.003 | Detects service ticket requests using weak encryption (RC4/0x17). |

---

## Built-In Hunter Directives (Non-Negotiable)

Every hunt produces **guidance alongside results**, not after. A dedicated `HunterDirective` column is hardcoded into every query output.

**Directives answer:**
1.  **Why** this fired (Context).
2.  **What** makes it suspicious (Risk Score).
3.  **What** to check next (Triage Step).

*Example Output:*
> *"HIGH SEVERITY: scrcons.exe loaded script engine AND beaconed to network. Immediate Action: Check for decoded payload in WMI Class."*

---

## Logic Visualization: Why "Composite" Matters

The diagram below illustrates **DLL Sideloading** — a technique where single events are insufficient, justifying the use of a Behavioural Chain.

```mermaid
graph LR
    subgraph "The Noise Problem"
    A[DLL dropped to writable path] -->|Too Common| Fail[False Positive]
    B[Signed Binary Loads DLL] -->|Standard OS Behavior| Fail
    end

    subgraph "The Composite Solution"
    C[Event 1: DLL Write (Temp/Public)] --> D{Correlation Time Window}
    E[Event 2: Signed Binary Load] --> D
    D -->|Match| F[High Confidence Alert]
    F --> G[Check Network/Persistence]
    end

    style Fail fill:#ff9999,stroke:#333,stroke-width:2px
    style F fill:#99ff99,stroke:#333,stroke-width:2px
```
