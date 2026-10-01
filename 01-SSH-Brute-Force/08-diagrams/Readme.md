# Project 01 — Diagrams

## CatchMe — Linux SSH Brute Force Detection

This directory contains the visual documentation for **Project 01: Linux SSH Brute Force Detection**.

The diagrams provide a visual representation of the complete SOC investigation, including the lab architecture, attack path, telemetry flow, detection engineering, investigation timeline, evidence correlation, detection logic, and complete end-to-end workflow.

---

## Diagram Index

### 01 — Lab Architecture

**File:** `01-lab-architecture.png`

Provides the foundational architecture of the CatchMe Linux SOC lab.

Includes:

- Kali attacker
- Linux SOC endpoint
- Elastic SIEM
- Network relationships
- Attack and monitoring components

Lab mapping:

```text
Kali
192.168.1.10
     |
     | SSH Attack
     ↓
soc-linux
192.168.1.16
     |
     | Telemetry
     ↓
elastic-siem
192.168.1.11
```
---

### 02 — Attack Flow

**File:** `02-attack-flow.png`

Visualizes the controlled SSH brute-force simulation.

```text
Kali
  ↓
Hydra
  ↓
SSH TCP/22
  ↓
soc-linux
  ↓
Repeated Authentication Failures
```

The diagram represents the attacker-side activity performed during Project 01.

---

### 03 — Telemetry Pipeline

**File:** `03-telemetry-pipeline.png`

Shows how the SSH activity becomes security telemetry and reaches the Elastic SOC environment.

```text
SSH Activity
     ↓
Linux SSH Authentication Logs
     ↓
Elastic Agent
     ↓
Elastic Platform
     ↓
Elasticsearch
     ↓
Kibana / SOC Analyst
```

---

### 04 — Detection Workflow

**File:** `04-Detection workflow.png`

Shows the detection engineering process used to convert observed SSH authentication behavior into a working Elastic detection.

```text
Observed Activity
       ↓
Telemetry Validation
       ↓
Threat Hunting
       ↓
Reliable Fields
       ↓
Detection Logic
       ↓
Threshold Rule
       ↓
Attack Validation
       ↓
Alert Generated
```

---

### 05 — Investigation Timeline & Log Correlation

**File:** `05-Investigation Timeline & Log Correlation.png`

Provides a chronological view of the attack and corresponding telemetry.

The diagram correlates:

* Attack execution
* SSH authentication failures
* Linux journal events
* Elastic event timestamps
* Detection alert generation

It also helps explain the UTC/IST timestamp relationship observed during the investigation.

---

### 06 — Sequence Flow

**File:** `06-Sequence Flow.png`

Shows the chronological interaction between the attacker, Linux endpoint, Elastic telemetry pipeline, detection engine, and SOC analyst.

```text
Attacker
   ↓
SSH Request
   ↓
Linux SSH Service
   ↓
Authentication Failure
   ↓
Elastic Agent
   ↓
Elastic SIEM
   ↓
Detection Rule
   ↓
Alert
   ↓
SOC Investigation
```

---

### 07 — Attack Timeline & Evidence Correlation

**File:** `07-Attack Timeline & Evidence Correlation.png`

Correlates attack activity with the evidence collected from different sources.

Evidence sources include:

* Kali attack execution
* Linux SSH journal
* Elastic authentication events
* Source IP
* User
* Process
* Detection alert
* Alert timestamps

The purpose is to demonstrate how multiple evidence sources support the same incident timeline.

---

### 08 — Detection Rule Logic & Flow

**File:** `08-Detection Rule Logic & Flow.png`

Visualizes the logic behind the Project 01 detection rule.

Core logic:

```text
event.action = authentication_failure
          +
process.name = sshd
          +
host.name = soc-linux
          +
Group by source.ip
          +
Threshold >= 3
          ↓
SSH Brute Force Detection
          ↓
Elastic Alert
```

This diagram demonstrates the transition from a hunting query to detection engineering.

---

### 09 — Investigation Workflow & Evidence Mapping

**File:** `09-Investigation Workflow & Evidence Mapping.png`

Shows how the SOC analyst moves from an alert to validated evidence.

```text
Alert
 ↓
Validate Alert
 ↓
Identify Source IP
 ↓
Identify Target Host
 ↓
Identify User
 ↓
Review SSH Events
 ↓
Review Linux Journal
 ↓
Build Timeline
 ↓
Correlate Evidence
 ↓
MITRE ATT&CK Mapping
 ↓
Incident Classification
```

---

### 10 — Complete End-to-End Visual

**File:** `10- Complete End-to-End Visual.png`

Provides a complete visual representation of Project 01.

```text
ATTACK
  ↓
SSH ACTIVITY
  ↓
ENDPOINT TELEMETRY
  ↓
ELASTIC SIEM
  ↓
THREAT HUNTING
  ↓
KQL
  ↓
DETECTION ENGINEERING
  ↓
ALERT
  ↓
INVESTIGATION
  ↓
EVIDENCE CORRELATION
  ↓
MITRE ATT&CK
  ↓
INCIDENT RESPONSE
  ↓
REMEDIATION
  ↓
LESSONS LEARNED
```

This is the primary high-level workflow diagram for the project.

---

### 11 — Complete Lab Architecture & Data Flow

**File:** `11-Complete Lab Architecture & Data Flow.png`

Combines the laboratory architecture with the security-data flow.

It represents:

* Attacker infrastructure
* Linux endpoint
* SSH service
* Endpoint telemetry
* Elastic Agent
* Elastic SIEM
* Detection
* Alerting
* Investigation
* SOC analyst workflow

The diagram provides the relationship between the physical/logical lab architecture and the SOC monitoring pipeline.

---

# Diagram Coverage

The Project 01 diagram set covers the following areas:

| Area                    | Covered |
| ----------------------- | ------- |
| Lab Architecture        | Yes     |
| Attack Flow             | Yes     |
| Telemetry Pipeline      | Yes     |
| Detection Engineering   | Yes     |
| Investigation Timeline  | Yes     |
| Log Correlation         | Yes     |
| Sequence Flow           | Yes     |
| Evidence Correlation    | Yes     |
| Detection Rule Logic    | Yes     |
| Investigation Workflow  | Yes     |
| End-to-End SOC Workflow | Yes     |
| Complete Data Flow      | Yes     |

---

# Evidence Accuracy

All diagrams must represent the activity actually demonstrated during Project 01.

Confirmed activity:

* Kali attacker: `192.168.1.10`
* Linux endpoint: `soc-linux`
* Linux endpoint IP: `192.168.1.16`
* Elastic SIEM: `elastic-siem`
* Elastic SIEM IP: `192.168.1.11`
* SSH service targeted on TCP/22
* Source IP observed as `192.168.1.10`
* Target account: `socadmin`
* Process: `sshd`
* Event action: `authentication_failure`
* Four matching authentication-failure events were observed during the detection-validation window.
* Elastic threshold detection generated an alert.
* Alert severity: Medium
* Risk score: 47
* Threshold result count: 4

## Important Investigation Result

The Project 01 evidence demonstrates **repeated failed SSH authentication attempts**.

It does **not** demonstrate:

* Successful password compromise
* Successful SSH login from the attack
* Privilege escalation
* Persistence
* Command and Control
* Post-compromise execution
* Host compromise

Therefore, the diagrams must not visually represent a successful compromise that was not demonstrated by the collected evidence.

---

# Diagram Naming Convention

All diagram filenames use numbered prefixes to maintain a consistent visual order in GitHub.

```text
01-...
02-...
03-...
...
11-...
```

This allows the diagrams to be viewed in the same logical order as the SOC investigation.

---

# Recommended Viewing Order

```text
01 Lab Architecture
        ↓
02 Attack Flow
        ↓
03 Telemetry Pipeline
        ↓
04 Detection Workflow
        ↓
05 Investigation Timeline & Log Correlation
        ↓
06 Sequence Flow
        ↓
07 Attack Timeline & Evidence Correlation
        ↓
08 Detection Rule Logic & Flow
        ↓
09 Investigation Workflow & Evidence Mapping
        ↓
10 Complete End-to-End Visual
        ↓
11 Complete Lab Architecture & Data Flow
```


