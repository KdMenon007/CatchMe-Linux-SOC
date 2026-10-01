# CatchMe Linux SOC — Project 01
## SSH Brute Force → Detection & Investigation

A real-time Linux SOC investigation demonstrating controlled SSH password-guessing, endpoint telemetry, KQL threat hunting, detection engineering, alert investigation, evidence correlation, MITRE ATT&CK mapping, Cyber Kill Chain analysis, and incident response documentation.

---

# Project Overview

| Field | Details |
|---|---|
| **Project** | CatchMe Linux SOC — Project 01 |
| **Scenario** | SSH Brute Force / Password Guessing |
| **Target** | `soc-linux` — `192.168.1.16` |
| **Attacker** | Kali Linux — `192.168.1.10` |
| **SIEM** | `elastic-siem` — `192.168.1.11` |
| **Target Service** | SSH / TCP 22 |
| **Target Account** | `socadmin` |
| **Detection** | `CatchMe - Linux SSH Brute Force Detection` |
| **Detection Type** | Threshold |
| **Threshold** | `>= 3` events grouped by `source.ip` |
| **Severity** | Medium |
| **Risk Score** | 47 |
| **Detection Result** | Successful |
| **Compromise Result** | Not demonstrated |

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
EVIDENCE
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
│ Authentication      │
│ System Logs         │
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

Detailed visual documentation is available in:

```text
08-diagrams/
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

A controlled SSH password-guessing simulation was performed from:

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

Hydra completed the validation with:

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

The activity was performed exclusively against the controlled CatchMe laboratory environment.

---

# 5. Attack Execution

Attack path:

```text
Kali Linux
192.168.1.10
      │
      │ Hydra
      ▼
SSH / TCP 22
      │
      ▼
soc-linux
192.168.1.16
      │
      ▼
socadmin
      │
      ▼
Authentication Failures
```

Attack evidence:

```text
09-Screenshots/Attack/
11-Evidence/Raw/hydra-attack-output.md
```

---

# 6. Endpoint Telemetry

The Linux endpoint generated SSH authentication telemetry during the controlled attack.

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
09-Screenshots/Telemetry/
11-Evidence/Raw/linux-ssh-journal.md
11-Evidence/Raw/elastic-authentication-events.md
```

---

# 7. Threat Hunting

Threat hunting was performed before relying on the final detection rule.

Elastic Discover and KQL were used to isolate the relevant authentication activity.

## Primary Hunt

```kql
event.action : "authentication_failure"
and source.ip : "192.168.1.10"
and host.name : "soc-linux"
```

Result:

```text
4 matching events
```

during the isolated detection-validation window.

## SSH Process Correlation

```kql
process.name : "sshd"
and source.ip : "192.168.1.10"
and host.name : "soc-linux"
```

This provided additional SSH process context.

Detailed queries:

```text
10-Queries/KQL/
10-Queries/supporting-queries/
```

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

The investigation deliberately separated:

```text
Observed Evidence
```

from:

```text
Assumptions
```

Only fields confirmed in the actual Elastic events were used for the final investigation.

Threat-hunting documentation:

```text
03-threat-hunting/threat-hunting.md
```

Supporting visual documentation:

```text
08-diagrams/13-KQL Hunting Flow.png
08-diagrams/14-Threat Hunting Flow.png
```

---

# 9. Detection Engineering

A dedicated Elastic threshold detection was created from the observed authentication behavior.

## Detection Name

```text
CatchMe - Linux SSH Brute Force Detection
```

## Query

```kql
event.action : "authentication_failure"
and process.name : "sshd"
and host.name : "soc-linux"
```

## Aggregation

```text
Group by:
source.ip
```

## Threshold

```text
>= 3 events
```

## Schedule

```text
Every 1 minute
Look-back: 1 minute
```

## Severity

```text
Medium
```

## Risk Score

```text
47
```

## Tags

```text
CatchMe
Linux
SSH
Brute-Force
T1110
Project-01
```

Detection documentation:

```text
04-detection/
```

---

# 10. Detection Validation

The detection rule was tested using the controlled SSH password-guessing scenario.

The validation generated:

```text
4 matching Elastic authentication_failure events
```

The threshold condition:

```text
source.ip
    +
>= 3 authentication failures
```

was satisfied.

Elastic generated the expected alert.

Detection result:

```text
Detection Rule       SUCCESSFUL
Matching Events      4
Source IP            192.168.1.10
Severity             Medium
Risk Score           47
Alert Status         Open
```

Detection evidence:

```text
09-Screenshots/Detection/
11-Evidence/Raw/detection-alert.md
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
Linux SSH Journal
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

Investigation documentation:

```text
05-investigation/
```

Raw investigation evidence:

```text
11-Evidence/Raw/Investigation Results.md
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

Full investigation timeline:

```text
05-investigation/timeline.md
```

Timeline visual:

```text
08-diagrams/05-Investigation Timeline & Log Correlation.png
08-diagrams/07-Attack Timeline & Evidence Correlation.png
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
Kali Linux
Asia/Kolkata
```

The timestamps were normalized during the investigation.

Example:

```text
03:27:17 UTC
=
08:57:17 IST
```

This allowed the attack execution, Linux SSH journal, Elastic events, and detection alert to be correlated accurately.

Supporting query:

```text
10-Queries/supporting-queries/Time Correlation Query.md
```

---

# 14. MITRE ATT&CK Mapping

## T1110.001 — Brute Force: Password Guessing

```text
Status: Observed
```

Evidence:

* Repeated password attempts
* SSH authentication failures
* Source IP `192.168.1.10`
* Target account `socadmin`
* Detection generated

## T1021.004 — Remote Services: SSH

```text
Status: Supporting Context
```

SSH was the remote service targeted by the password-guessing activity.

Successful remote access was not demonstrated.

## T1078 — Valid Accounts

```text
Status: Not Observed
```

No valid credential was successfully used during the simulation.

MITRE documentation:

```text
06-mitre/mitre-attack.md
```

MITRE visual:

```text
08-diagrams/15-MITRE ATT&CK Mapping.png
```

---

# 15. Cyber Kill Chain

| Phase                 | Project Status                         |
| --------------------- | -------------------------------------- |
| Reconnaissance        | Lab preparation / controlled targeting |
| Weaponization         | Controlled password list prepared      |
| Delivery              | SSH authentication traffic delivered   |
| Exploitation          | Repeated password attempts             |
| Installation          | Not observed                           |
| Command & Control     | Not observed                           |
| Actions on Objectives | Not observed                           |

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

The project distinguishes between:

```text
Actions Actually Performed
```

and:

```text
Recommended SOC Response Actions
```

These are intentionally kept separate.

Incident-response documentation:

```text
07-incident-response/
```

---

# 17. Containment

The source was identified as:

```text
192.168.1.10
```

and confirmed as the controlled CatchMe attacker.

No production-style network blocking or account disabling was performed because:

* The source was an authorized lab attacker.
* The activity was controlled.
* No successful compromise was demonstrated.

Evidence was preserved for investigation and documentation.

Containment documentation:

```text
07-incident-response/containment.md
```

---

# 18. Remediation

Recommended defensive improvements include:

* Prefer key-based SSH authentication where appropriate.
* Restrict SSH exposure.
* Limit SSH access to trusted management networks.
* Protect administrative accounts.
* Evaluate brute-force rate limiting.
* Maintain authentication telemetry.
* Correlate repeated failures with subsequent successful logins.
* Tune thresholds against legitimate baseline activity.
* Monitor unusual source-IP authentication patterns.
* Review SSH authentication configuration regularly.

These are remediation recommendations and are not represented as completed production changes.

Documentation:

```text
07-incident-response/remediation.md
```

---

# 19. Lessons Learned

Project 01 demonstrated several SOC investigation principles.

### Baseline Before Attack

Normal authentication activity should be understood before attributing events to an attack.

### Hunting Before Detection

Raw events should be investigated before converting a hunting query into detection logic.

### Evidence Before Attribution

The analyst should correlate:

```text
Source
+
User
+
Process
+
Host
+
Timestamp
+
Related Events
```

### Detection Does Not Equal Compromise

An authentication attack can be detected even when the attacker fails to authenticate.

### ATT&CK Mapping Must Follow Evidence

Only techniques supported by observed activity should be marked as observed.

### Timezones Matter

Multi-system investigations require verified timestamp normalization.

Full documentation:

```text
07-incident-response/lessons-learned.md
```

---

# 20. Evidence Structure

```text
11-Evidence/
├── Raw/
│   ├── detection-alert.md
│   ├── elastic-authentication-events.md
│   ├── hydra-attack-output.md
│   ├── Investigation Results.md
│   └── linux-ssh-journal.md
└── Hashes/
    └── Hashes.md
```

Evidence is separated from screenshots and project documentation.

Sensitive information such as real credentials, passwords, private keys, and unnecessary system identifiers must never be committed to GitHub.

---

# 21. Evidence Integrity

SHA-256 hashes were generated for the raw evidence artifacts.

The recorded hashes are maintained in:

```text
11-Evidence/Hashes/Hashes.md
```

This provides a basic integrity reference for the evidence files used in the investigation.

---

# 22. Screenshots

```text
09-Screenshots/
├── Attack/
├── Telemetry/
├── hunting/
├── Detection/
└── Investigation/
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

Each screenshot category contains its own `Readme.md` describing the captured evidence.

---

# 23. Queries

```text
10-Queries/
├── KQL/
│   ├── Authentication Failure Hunt.md
│   ├── Detection Rule Query.md
│   ├── Investigation Query.md
│   └── ssh-process-correlation.md
│
└── supporting-queries/
    ├── Endpoint Validation Query.md
    ├── linux-ssh-journal.md
    └── Time Correlation Query.md
```

The queries are separated into:

* KQL hunting and detection queries
* Supporting Linux and validation queries
* Investigation and time-correlation queries

This structure allows the detection and investigation logic to be reviewed and reused.

---

# 24. Diagrams

Project 01 contains a dedicated visual documentation layer.

```text
08-diagrams/
├── 01-lab-architecture.png
├── 02-attack-flow.png
├── 03-telemetry-pipeline.png
├── 04-Detection workflow.png
├── 05-Investigation Timeline & Log Correlation.png
├── 06-Sequence Flow.png
├── 07-Attack Timeline & Evidence Correlation.png
├── 08-Detection Rule Logic & Flow.png
├── 09-Investigation Workflow & Evidence Mapping.png
├── 10- Complete End-to-End Visual.png
├── 11-Complete Lab Architecture & Data Flow.png
├── 12-SOC Infographic Poster.png
├── 13-KQL Hunting Flow.png
├── 14-Threat Hunting Flow.png
├── 15-MITRE ATT&CK Mapping.png
└── Readme.md
```

The 15-diagram set covers:

* Lab architecture
* Attack flow
* Telemetry pipeline
* Detection workflow
* Investigation timeline
* Sequence flow
* Attack/evidence correlation
* Detection rule logic
* Investigation workflow
* End-to-end SOC workflow
* Complete architecture/data flow
* SOC project overview
* KQL hunting
* Threat hunting
* MITRE ATT&CK mapping

---

# 25. Repository Structure

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
│   └── detection-rule-export.md
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
├── 08-diagrams/
│   ├── 01-lab-architecture.png
│   ├── 02-attack-flow.png
│   ├── 03-telemetry-pipeline.png
│   ├── 04-Detection workflow.png
│   ├── 05-Investigation Timeline & Log Correlation.png
│   ├── 06-Sequence Flow.png
│   ├── 07-Attack Timeline & Evidence Correlation.png
│   ├── 08-Detection Rule Logic & Flow.png
│   ├── 09-Investigation Workflow & Evidence Mapping.png
│   ├── 10- Complete End-to-End Visual.png
│   ├── 11-Complete Lab Architecture & Data Flow.png
│   ├── 12-SOC Infographic Poster.png
│   ├── 13-KQL Hunting Flow.png
│   ├── 14-Threat Hunting Flow.png
│   ├── 15-MITRE ATT&CK Mapping.png
│   └── Readme.md
│
├── 09-Screenshots/
│   ├── Attack/
│   ├── Telemetry/
│   ├── hunting/
│   ├── Detection/
│   └── Investigation/
│
├── 10-Queries/
│   ├── KQL/
│   └── supporting-queries/
│
├── 11-Evidence/
│   ├── Raw/
│   └── Hashes/
│
└── 12-Assets/
    ├── project-banner.png
    └── Readme.md
```

---

# 26. Project Evidence Chain

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
THREAT HUNTING    DETECTION
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
       │
       ▼
  EVIDENCE / REPORT
```

---

# 27. Final Investigation Finding

The collected evidence supports the following conclusion:

```text
Controlled SSH Password-Guessing Activity
                    ↓
Multiple Authentication Failures
                    ↓
Linux Endpoint Telemetry Generated
                    ↓
Elastic Telemetry Received
                    ↓
KQL Hunting Identified Activity
                    ↓
Threshold Detection Triggered
                    ↓
SOC Alert Generated
                    ↓
Alert Investigated
                    ↓
Evidence Correlated
```

The available evidence does **not** demonstrate:

```text
Successful Password Authentication
Successful SSH Compromise
Privilege Escalation
Persistence
Command and Control
Data Access
Data Exfiltration
Host Impact
```

Therefore, Project 01 is classified as a **detected SSH brute-force/password-guessing attempt without demonstrated successful compromise**.

---

# 28. Project Status

| Component                 | Status                 |
| ------------------------- | ---------------------- |
| Lab verification          | Complete               |
| Pre-attack baseline       | Complete               |
| Attack simulation         | Complete               |
| Endpoint telemetry        | Complete               |
| Threat hunting            | Complete               |
| KQL investigation         | Complete               |
| Detection engineering     | Complete               |
| Detection validation      | Complete               |
| Alert investigation       | Complete               |
| Timeline                  | Complete               |
| MITRE ATT&CK              | Complete               |
| Cyber Kill Chain          | Complete               |
| Incident report           | Complete               |
| Containment documentation | Complete               |
| Remediation documentation | Complete               |
| Lessons learned           | Complete               |
| Evidence collection       | Complete               |
| Evidence hashes           | Complete               |
| Screenshots               | Complete               |
| Queries                   | Complete               |
| Diagrams                  | Complete — 15 diagrams |
| Project banner            | Complete               |
| GitHub packaging          | Complete               |

---

# 29. Security & Ethics

This project is a controlled cybersecurity laboratory exercise.

All attack simulations are performed against systems owned or explicitly authorized for testing within the CatchMe lab.

No credentials, private keys, sensitive production data, or unauthorized systems should be targeted.

Credential-access and password-guessing exercises remain limited to controlled laboratory systems.

Evidence committed to GitHub must be sanitized and must not contain:

* Real passwords
* Private keys
* Authentication tokens
* Production credentials
* Sensitive personal information
* Unnecessary infrastructure secrets

---

# 30. CatchMe SOC Philosophy

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
      ↓
What evidence supports the conclusion?
```

---

# Project 01 Result

**SSH Brute Force Detection: VALIDATED**

**Telemetry: VALIDATED**

**Threat Hunting: COMPLETED**

**Detection Engineering: COMPLETED**

**Alert Generation: VALIDATED**

**Investigation: COMPLETED**

**Evidence Correlation: COMPLETED**

**MITRE ATT&CK Mapping: COMPLETED**

**Cyber Kill Chain Analysis: COMPLETED**

**Incident Response Documentation: COMPLETED**

**Evidence Hashing: COMPLETED**

**Diagrams: COMPLETED — 15**

**GitHub Packaging: COMPLETED**

**Successful Compromise: NOT DEMONSTRATED**

---

## CatchMe Linux SOC

**Project 01 — SSH Brute Force → Detection & Investigation**

```text
ATTACK → TELEMETRY → HUNT → DETECT → ALERT → INVESTIGATE → RESPOND → DOCUMENT
```


