# CatchMe Linux SOC — Project 01
## SSH Brute Force → Detection & Investigation

> A real-time Linux SOC investigation demonstrating controlled SSH password-guessing, endpoint telemetry, KQL threat hunting, detection engineering, alert investigation, MITRE ATT&CK mapping, Cyber Kill Chain analysis, and incident response documentation.

---

## Project Overview

**Project:** CatchMe Linux SOC — Project 01  
**Scenario:** SSH Brute Force / Password Guessing  
**Target:** `soc-linux` — `192.168.1.16`  
**Attacker:** Kali Linux — `192.168.1.10`  
**SIEM:** `elastic-siem` — `192.168.1.11`  
**Target Service:** SSH / TCP 22  
**Target Account:** `socadmin`  
**Detection:** `CatchMe - Linux SSH Brute Force Detection`  
**Detection Type:** Threshold  
**Severity:** Medium  
**Risk Score:** 47  
**Result:** Detection successful; successful compromise not demonstrated

---

# 1. Objective

The objective of Project 01 is to demonstrate a complete SOC workflow against a controlled SSH password-guessing scenario.

The project follows:

```text
ATTACK
   ↓
ENDPOINT TELEMETRY
   ↓
ELASTIC AGENT
   ↓
ELASTIC SIEM
   ↓
THREAT HUNTING
   ↓
KQL CORRELATION
   ↓
DETECTION ENGINEERING
   ↓
ALERT
   ↓
SOC INVESTIGATION
   ↓
TIMELINE
   ↓
MITRE ATT&CK
   ↓
CYBER KILL CHAIN
   ↓
INCIDENT RESPONSE
   ↓
CONTAINMENT
   ↓
REMEDIATION
   ↓
LESSONS LEARNED
   ↓
GITHUB PORTFOLIO
```
The project is designed as a hands-on SOC investigation rather than a static attack demonstration.

---

# 2. Lab Architecture

```text
                         CATCHME LAB NETWORK
                           192.168.1.0/24

┌─────────────────────┐
│     KALI LINUX      │
│      ATTACKER       │
│   192.168.1.10      │
│                     │
│ Hydra SSH Testing   │
└──────────┬──────────┘
           │
           │ SSH / TCP 22
           │ Password Guessing
           ▼
┌─────────────────────┐
│      SOC-LINUX      │
│    Linux Endpoint   │
│   192.168.1.16      │
│                     │
│ OpenSSH / sshd      │
│ System Logs         │
│ Authentication      │
│ Auditd              │
│ Elastic Agent       │
└──────────┬──────────┘
           │
           │ Telemetry
           ▼
┌─────────────────────┐
│    ELASTIC-SIEM     │
│   192.168.1.11      │
│                     │
│ Elasticsearch       │
│ Kibana              │
│ Elastic Security    │
│ Fleet Server        │
│ Detection Engine    │
└─────────────────────┘
```

Detailed architecture diagrams are stored in:

```text
diagrams/
```

---

# 3. Fixed Lab Mapping

| System         | Hostname       |               IP | Role                           |
| -------------- | -------------- | ---------------: | ------------------------------ |
| Kali Linux     | `kiran`        |   `192.168.1.10` | Attacker / attack simulation   |
| Linux Endpoint | `soc-linux`    |   `192.168.1.16` | Target Linux endpoint          |
| Elastic SIEM   | `elastic-siem` |   `192.168.1.11` | Elasticsearch + Kibana + Fleet |
| Gateway        | —              |    `192.168.1.1` | Lab gateway                    |
| Network        | —              | `192.168.1.0/24` | CatchMe lab network            |

---

# 4. Attack Scenario

A controlled password-guessing simulation was performed from:

```text
192.168.1.10
```

against:

```text
192.168.1.16:22
```

targeting:

```text
socadmin
```

The controlled password list contained five intentionally incorrect password candidates.

Hydra completed the test with:

```text
0 valid password found
```

Therefore:

```text
Brute-force activity        YES
Authentication failures     YES
Detection alert             YES
Valid credential discovered NO
Successful SSH compromise   NOT DEMONSTRATED
```

---

# 5. Attack Execution

The attack was performed only against the controlled CatchMe laboratory endpoint.

Attack path:

```text
Kali
192.168.1.10
      │
      │ Hydra
      ▼
SSH TCP/22
      │
      ▼
soc-linux
192.168.1.16
      │
      ▼
socadmin
      │
      ▼
Authentication failures
```

Evidence:

```text
screenshots/attack/
```

---

# 6. Endpoint Telemetry

The Linux endpoint generated SSH authentication telemetry during the attack.

Observed fields included:

```text
source.ip
user.name
event.action
process.name
host.name
```

Observed values included:

```text
source.ip    = 192.168.1.10
user.name    = socadmin
event.action = authentication_failure
process.name = sshd
host.name    = soc-linux
```

The Linux SSH journal independently recorded repeated authentication failures originating from the Kali attacker.

Telemetry evidence:

```text
screenshots/telemetry/
screenshots/investigation/
```

---

# 7. Threat Hunting

Threat hunting was performed before relying on the final detection rule.

The investigation used Elastic Discover and KQL to isolate the relevant authentication activity.

### Primary Hunt

```kql
event.action : "authentication_failure"
and source.ip : "192.168.1.10"
and host.name : "soc-linux"
```

Result:

```text
4 matching events
```

during the isolated detection-test window.

### SSH Correlation Hunt

```kql
process.name : "sshd"
and source.ip : "192.168.1.10"
and host.name : "soc-linux"
```

This provided additional SSH event context.

---

# 8. Hunting Methodology

The hunting workflow used in this project:

```text
Baseline
   ↓
Controlled Attack
   ↓
Raw Endpoint Telemetry
   ↓
Elastic Discover
   ↓
Broad Hunt
   ↓
Narrow KQL
   ↓
Field Validation
   ↓
Source Correlation
   ↓
Attack Finding
```

The hunting process deliberately separated:

```text
Observed Evidence
```

from:

```text
Assumptions
```

Only fields confirmed in the actual Elastic events were used for the final investigation.

Detailed hunting documentation:

```text
03-threat-hunting/threat-hunting.md
```

Hunting diagrams:

```text
diagrams/04-threat-hunting-flow.png
diagrams/05-kql-hunting-flow.png
```

---

# 9. Detection Engineering

A dedicated Elastic threshold detection was created from the observed authentication behavior.

### Detection Name

```text
CatchMe - Linux SSH Brute Force Detection
```

### Query

```kql
event.action : "authentication_failure"
and process.name : "sshd"
and host.name : "soc-linux"
```

### Aggregation

```text
Group by:
source.ip
```

### Threshold

```text
>= 3 events
```

### Schedule

```text
Every 1 minute
Additional look-back: 1 minute
```

### Severity

```text
Medium
```

### Risk Score

```text
47
```

### Tags

```text
CatchMe
Linux
SSH
Brute-Force
T1110
Project-01
```

---

# 10. Detection Validation

The detection rule was tested using the same controlled SSH password-guessing scenario.

The test generated:

```text
4 matching Elastic authentication_failure events
```

The threshold condition:

```text
source.ip
>= 3
```

was satisfied.

Elastic generated the expected alert.

Detection result:

```text
Detection Rule       SUCCESSFUL
Matching Events      4
Source IP             192.168.1.10
Severity              Medium
Risk Score            47
Alert Status          Open
```

Detection evidence:

```text
screenshots/detection/
```

---

# 11. Alert Investigation

The generated alert was investigated rather than treated as the final conclusion.

Investigation sequence:

```text
ALERT
  ↓
Source IP
  ↓
Target Host
  ↓
Target User
  ↓
SSH Process
  ↓
Raw Authentication Events
  ↓
Endpoint Journal
  ↓
Attack Timeline
  ↓
Successful Login Check
  ↓
Impact Assessment
```

The alert identified:

```text
Source:
192.168.1.10

Target:
soc-linux

Account:
socadmin

Process:
sshd

Event:
authentication_failure
```

---

# 12. Timeline

The final detection-test timeline was correlated across Kali, Linux, and Elastic.

| Time (IST)   | Event                                           |
| ------------ | ----------------------------------------------- |
| 08:57:17     | Hydra SSH test begins                           |
| 08:57:17     | Linux SSH authentication failures begin         |
| 08:57:19     | Failed password attempts continue               |
| 08:57:22     | Additional failed password attempts             |
| 08:57:23     | Elastic authentication-failure event observed   |
| 08:57:25     | Additional failed password activity             |
| 08:57:26     | SSH connection closes during pre-authentication |
| 08:57:26     | Hydra finishes with `0 valid password found`    |
| 08:57:26.518 | Elastic original event timestamp                |
| 08:58:02.247 | Detection alert generated                       |

Full timeline:

```text
05-investigation/timeline.md
```

Timeline diagram:

```text
diagrams/09-investigation-timeline.png
```

---

# 13. Time Correlation

The systems used different timezones.

```text
soc-linux
Etc/UTC
```

and:

```text
Kali
Asia/Kolkata
```

The timestamps were normalized during investigation.

Example:

```text
03:27:17 UTC
=
08:57:17 IST
```

This allowed the attack execution and Linux SSH journal to be correlated accurately.

---

# 14. MITRE ATT&CK Mapping

### T1110.001 — Brute Force: Password Guessing

```text
Status: Observed
```

Evidence:

* Repeated password attempts
* SSH authentication failures
* Source IP `192.168.1.10`
* Target account `socadmin`
* Detection generated

### T1021.004 — Remote Services: SSH

```text
Status: Supporting Context
```

SSH was the remote service targeted by the password-guessing activity.

Successful remote access was not demonstrated.

### T1078 — Valid Accounts

```text
Status: Not Observed
```

No valid credential was successfully used during the simulation.

Full mapping:

```text
06-mitre/mitre-attack.md
```

---

# 15. Cyber Kill Chain

| Phase                 | Project Status                       |
| --------------------- | ------------------------------------ |
| Reconnaissance        | Observed during lab preparation      |
| Weaponization         | Controlled password list prepared    |
| Delivery              | SSH authentication traffic delivered |
| Exploitation          | Repeated password attempts           |
| Installation          | Not observed                         |
| Command & Control     | Not observed                         |
| Actions on Objectives | Not observed                         |

The simulation did not progress to successful compromise.

---

# 16. Incident Response

The incident-response workflow documented for Project 01 is:

```text
Detection
   ↓
Validation
   ↓
Investigation
   ↓
Impact Assessment
   ↓
Containment Decision
   ↓
Evidence Preservation
   ↓
Remediation
   ↓
Lessons Learned
```

The project documents both:

```text
Actions actually performed
```

and:

```text
Recommended SOC response actions
```

These are intentionally kept separate.

---

# 17. Containment

The source was identified as:

```text
192.168.1.10
```

and confirmed as the controlled CatchMe attacker.

No production-style network blocking or account disabling was performed because this was a controlled lab simulation and no successful compromise was demonstrated.

Evidence was preserved.

Containment documentation:

```text
07-incident-response/containment.md
```

---

# 18. Remediation

Recommended defensive improvements include:

* Prefer key-based SSH authentication where appropriate
* Restrict SSH exposure
* Limit SSH access to trusted management networks
* Protect administrative accounts
* Evaluate brute-force rate limiting
* Maintain authentication telemetry
* Correlate repeated failures with subsequent successful logins
* Tune thresholds against legitimate baseline activity

These are remediation recommendations and are not represented as completed production changes.

Documentation:

```text
07-incident-response/remediation.md
```

---

# 19. Lessons Learned

Project 01 demonstrated several SOC investigation principles:

### Baseline Before Attack

Normal authentication activity must be understood before attributing events to an attack.

### Hunting Before Detection

Raw events should be investigated before converting a hunting query into detection logic.

### Evidence Before Attribution

The analyst should correlate source, user, process, host, timestamp, and related events.

### Detection Does Not Equal Compromise

An authentication attack can be detected even when the attacker fails to authenticate.

### ATT&CK Mapping Must Follow Evidence

Only techniques supported by observed activity should be mapped as observed.

### Timezones Matter

Multi-system investigations require verified timestamp normalization.

Full lessons learned:

```text
07-incident-response/lessons-learned.md
```

---

# 20. Evidence Structure

```text
evidence/
├── raw/
├── sanitized/
└── hashes/
```

Evidence is separated from screenshots and documentation.

Sensitive information such as real credentials, passwords, private keys, and unnecessary system identifiers should never be committed to GitHub.

---

# 21. Screenshots

```text
screenshots/
├── attack/
├── telemetry/
├── hunting/
├── detection/
└── investigation/
```

The screenshots document the actual SOC workflow:

```text
Attack
  ↓
Telemetry
  ↓
Hunting
  ↓
Detection
  ↓
Investigation
```

---

# 22. Queries

```text
queries/
├── kql/
└── supporting-queries/
```

KQL queries used during the project are stored separately so that the detection and hunting logic can be reused and reviewed.

---

# 23. Diagrams

Project 01 contains a dedicated visual documentation layer.

```text
diagrams/
├── 01-lab-architecture.png
├── 02-attack-flow.png
├── 03-telemetry-pipeline.png
├── 04-threat-hunting-flow.png
├── 05-kql-hunting-flow.png
├── 06-detection-engineering-flow.png
├── 07-detection-logic-flow.png
├── 08-alert-investigation-flow.png
├── 09-investigation-timeline.png
├── 10-event-correlation.png
├── 11-mitre-attack-mapping.png
├── 12-cyber-kill-chain.png
├── 13-incident-response-flow.png
├── 14-containment-flow.png
├── 15-remediation-flow.png
├── 16-evidence-flow.png
├── 17-soc-end-to-end-workflow.png
└── 18-project-01-overview.png
```

The diagram set intentionally includes:

* Architecture diagrams
* Attack-flow diagrams
* Telemetry pipelines
* Threat-hunting flows
* KQL investigation flows
* Detection-engineering flows
* Detection-logic diagrams
* Alert investigation flows
* Timelines
* Event-correlation diagrams
* MITRE ATT&CK mapping
* Cyber Kill Chain mapping
* Incident-response flows
* Containment flows
* Remediation flows
* Evidence flows
* End-to-end SOC workflows

---

# 24. Repository Structure

```text
01-SSH-Brute-Force/
│
├── README.md
│
├── 00-pre-attack/
│   ├── baseline-check.md
│   └── environment-verification.md
│
├── 01-attack/
│   └── attack-simulation.md
│
├── 02-telemetry/
│   ├── telemetry-validation.md
│   └── log-sources.md
│
├── 03-threat-hunting/
│   └── threat-hunting.md
│
├── 04-detection/
│   ├── detection.md
│   ├── detection-logic.md
│   └── detection-rule-export.ndjson
│
├── 05-investigation/
│   ├── investigation.md
│   ├── findings.md
│   └── timeline.md
│
├── 06-mitre/
│   └── mitre-attack.md
│
├── 07-incident-response/
│   ├── incident-report.md
│   ├── containment.md
│   ├── remediation.md
│   └── lessons-learned.md
│
├── diagrams/
│
├── screenshots/
│   ├── attack/
│   ├── telemetry/
│   ├── hunting/
│   ├── detection/
│   └── investigation/
│
├── queries/
│   ├── kql/
│   └── supporting-queries/
│
├── evidence/
│   ├── raw/
│   ├── sanitized/
│   └── hashes/
│
└── assets/
    └── project-banner.png
```

---

# 25. Project Evidence Chain

```text
KALI
192.168.1.10
     │
     │ SSH Password Guessing
     ▼
SOC-LINUX
192.168.1.16
     │
     │ SSH Authentication Failures
     ▼
ELASTIC AGENT
     │
     │ Telemetry
     ▼
ELASTIC SIEM
192.168.1.11
     │
     ├───────────────┐
     ▼               ▼
THREAT HUNTING   DETECTION
     │               │
     │ KQL           │ Threshold
     │               │
     └───────┬───────┘
             ▼
           ALERT
             │
             ▼
      SOC INVESTIGATION
             │
       ┌─────┼─────┐
       ▼     ▼     ▼
    Timeline Source User
       │
       ▼
  MITRE ATT&CK
       │
       ▼
INCIDENT RESPONSE
       │
       ▼
 REMEDIATION
       │
       ▼
LESSONS LEARNED
```

---

# 26. Final Investigation Finding

The evidence supports the following conclusion:

```text
Controlled SSH password-guessing activity
                    ↓
Multiple authentication failures
                    ↓
Elastic telemetry received
                    ↓
KQL hunting identified activity
                    ↓
Threshold detection triggered
                    ↓
SOC alert generated
                    ↓
Investigation completed
```

The available evidence does **not** demonstrate:

```text
Successful password authentication
Successful SSH compromise
Persistence
Command and Control
Data access
Data exfiltration
Host impact
```

---

# 27. Project Status

| Component                 | Status      |
| ------------------------- | ----------- |
| Lab verification          | Complete    |
| Pre-attack baseline       | Complete    |
| Attack simulation         | Complete    |
| Endpoint telemetry        | Complete    |
| Threat hunting            | Complete    |
| KQL investigation         | Complete    |
| Detection engineering     | Complete    |
| Detection validation      | Complete    |
| Alert investigation       | Complete    |
| Timeline                  | Complete    |
| MITRE ATT&CK              | Complete    |
| Cyber Kill Chain          | Complete    |
| Incident report           | Complete    |
| Containment documentation | Complete    |
| Remediation documentation | Complete    |
| Lessons learned           | Complete    |
| Diagrams                  | In progress |
| GitHub packaging          | In progress |

---

# 28. Security & Ethics

This project is a controlled cybersecurity laboratory exercise.

All attack simulations are performed against systems owned or explicitly authorized for testing within the CatchMe lab.

No credentials, private keys, sensitive production data, or unauthorized systems should be targeted.

Credential-access and password-guessing exercises remain limited to controlled laboratory systems.

---

# 29. CatchMe SOC Philosophy

```text
Every Alert Tells a Story.
```

The objective is not simply to generate an alert.

The objective is to demonstrate the complete analyst process:

```text
What happened?
      ↓
How did it happen?
      ↓
What telemetry proves it?
      ↓
How was it detected?
      ↓
What else happened around it?
      ↓
Did compromise actually occur?
      ↓
What ATT&CK techniques apply?
      ↓
What should the SOC do?
      ↓
How can detection be improved?
```

---

# Project 01 Result

**SSH Brute Force Detection: VALIDATED**

**Telemetry: VALIDATED**

**Threat Hunting: COMPLETED**

**Detection Engineering: COMPLETED**

**Alert Generation: VALIDATED**

**Investigation: COMPLETED**

**MITRE ATT&CK Mapping: COMPLETED**

**Incident Response Documentation: COMPLETED**

**Successful Compromise: NOT DEMONSTRATED**

---

## CatchMe Linux SOC

**Project 01 — SSH Brute Force → Detection & Investigation**

```text
ATTACK → TELEMETRY → HUNT → DETECT → ALERT → INVESTIGATE → RESPOND → DOCUMENT
```


