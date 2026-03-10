# ATT&CK Substrate Adjacency — Extending ATT&CK with Ecosystem Detection

**Author:** Ala Dabat  
**Framework:** Minimum Truth Detection Framework  
**Concept:** Substrate Adjacency / Cousin Technique Doctrine  
**Purpose:** Explain how the framework addresses a structural coverage limitation in MITRE ATT&CK.

---

# The Structural Gap in ATT&CK

MITRE ATT&CK is a powerful framework describing **what adversaries do** during intrusions.

It provides:

- Technique taxonomy
- Sub-technique depth
- Attack lifecycle positioning

This produces **vertical modelling of attacker behaviour**.

Example:

```
Technique
└── Sub-technique
```

However, ATT&CK does **not model horizontal adjacency between techniques operating on different substrates**.

---

# Example: Lateral Movement Substrates

Consider the following ATT&CK techniques.

| Technique | Protocol | Substrate |
|---|---|---|
| T1021.002 | SMB | Admin shares |
| T1021.003 | DCOM | COM subsystem |
| T1021.006 | WinRM | WSMan |
| T1047 | WMI | WMI subsystem |

ATT&CK represents these as **separate techniques**.

Operationally they are often **the same adversary intent executed across different substrates**.

Example real attacker pivot:

```
SMB attempt
↓ blocked
WinRM attempt
↓ blocked
DCOM attempt
↓ success
```

ATT&CK logs **three techniques**.

In reality this is **one objective expressed across three execution substrates**.

---

# The Coverage Illusion

Security programs often measure detection maturity like this:

> “Do we detect technique T1021.002?”

If the answer is yes, the technique is marked as covered.

But detecting **SMB lateral movement** does not imply detection of:

- WinRM lateral movement
- WMI lateral movement
- DCOM lateral movement

Yet attackers frequently pivot between these during real intrusions.

This produces **false coverage confidence**.

---

# What ATT&CK Lacks

ATT&CK currently models:

```
Technique
↓
Sub-technique
```

This provides **vertical modelling**.

What is missing is **horizontal technique adjacency across system substrates**.

Conceptually:

```
           Lateral Movement Intent
                    │
      ┌─────────────┼─────────────┐
      │             │             │
    SMB           WinRM          DCOM
  (T1021.002)   (T1021.006)    (T1021.003)
```

All three represent the **same attacker goal**.

---

# Substrate Adjacency

The **Minimum Truth Detection Framework** introduces the concept of **substrate adjacency**.

Definition:

> Substrate adjacency describes how a single adversary objective can manifest across multiple operating system mechanisms.

Examples of substrates:

- SMB
- WinRM
- WMI
- RPC
- DCOM

Each substrate is a **different technical surface for executing the same attacker behaviour**.

---

# Cousin Technique Doctrine

Within the framework, adjacent techniques are referred to as **Cousins**.

Definition:

> Cousin techniques are ATT&CK techniques that represent the same adversary intent executed on different system substrates.

Example mapping:

| Intent | Technique | Substrate |
|---|---|---|
| Lateral Movement | SMB Admin Share | SMB |
| Lateral Movement | WinRM Remote Exec | WSMan |
| Lateral Movement | WMI Exec | WMI |
| Lateral Movement | DCOM Exec | COM |

These are **not separate adversary behaviours**.

They are **alternate implementations of the same objective**.

---

# Why This Matters for Detection

Detection rules written for one substrate do not automatically detect its cousins.

Example detection focused on SMB.

```kql
DeviceNetworkEvents
| where RemotePort == 445
| where InitiatingProcessFileName in~ ("psexec.exe","wmic.exe")
```

This detects **SMB-based lateral movement**.

However, an attacker may pivot to:

- WinRM
- DCOM
- WMI

Without triggering the rule.

---

# Ecosystem Detection

The framework therefore models attacks as **ecosystems rather than isolated techniques**.

Instead of:

```
Technique Detection
```

It models:

```
Intent
↓
Substrate Variants
↓
Composite Detection Coverage
```

Conceptually:

```
             Adversary Intent
                    │
        ┌───────────┼───────────┐
        │           │           │
      SMB         WinRM        DCOM
        │           │           │
   Detection   Detection   Detection
```

Detection maturity therefore becomes:

```
Intent Coverage
not
Technique Coverage
```

---

# Practical Detection Engineering Impact

A detection engineer should ask:

> “If we detect this technique, what are its cousins?”

Example reasoning:

```
Detect SMB lateral movement
→ what about WinRM?
→ what about WMI?
→ what about DCOM?
```

Failure to map cousin techniques creates **coverage gaps attackers exploit naturally**.

---

# Relationship to ATT&CK

This model does **not replace ATT&CK**.

Instead it extends ATT&CK with an additional layer:

```
ATT&CK
Technique taxonomy
+
Minimum Truth Framework
Substrate adjacency modelling
```

ATT&CK provides the **behaviour catalogue**.

The framework provides **operational detection coverage modelling**.

---

# Key Takeaway

ATT&CK answers:

> “What techniques do attackers use?”

The Minimum Truth Detection Framework additionally asks:

> “What adjacent substrates can attackers pivot to when this technique fails?”

Understanding that adjacency transforms detection engineering from:

```
Technique coverage
```

into

```
Adversary intent coverage
```

---

# Framework Implication

This concept enables:

- systematic mapping of cousin techniques
- realistic coverage modelling
- stronger detection architecture
- reduced blind spots in lateral movement and execution surfaces

It is therefore a **detection coverage adjacency model built on top of ATT&CK**.

---

*Part of the Minimum Truth Detection Framework documentation.*
