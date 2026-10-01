# Lessons Learned

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Incident ID:** `CATCHME-LNX-001`  
**Scenario:** SSH Brute Force Detection  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Service:** SSH (`TCP/22`)

---

# 1. Objective

This document records the lessons learned from Project 01.

The objective is to identify:

- what worked
- what was validated
- what investigation challenges were encountered
- what detection improvements are possible
- what should be carried forward into Project 02

The project demonstrated a complete SOC workflow:

```text
Attack
↓
Telemetry
↓
Threat Hunting
↓
Detection Engineering
↓
Alert
↓
Investigation
↓
MITRE ATT&CK
↓
Cyber Kill Chain
↓
Incident Response
↓
Remediation
↓
Lessons Learned
```
---

# 2. What Worked

## 2.1 Lab Baseline

The Linux endpoint was baselined before the attack.

The baseline established:

```text
Hostname
Network configuration
Running services
Users and privileges
SSH configuration
Persistence
Auditd
Elastic Agent
Fleet connectivity
```

This provided a known-good reference before attack simulation.

---

## 2.2 Telemetry Collection

Linux SSH authentication activity was successfully collected by Elastic.

Important investigation fields included:

```text
event.action
source.ip
user.name
process.name
host.name
@timestamp
```

These fields were sufficient to correlate:

```text
Source
+
Target
+
Account
+
Process
+
Authentication Result
+
Time
```

---

## 2.3 Threat Hunting

The investigation started with broad queries and then narrowed the search.

The hunting progression was:

```text
Host
↓
Authentication activity
↓
Source IP
↓
SSH process
↓
User
↓
Attack time window
```

This prevented the investigation from relying on a single field or assumption.

---

## 2.4 Detection Engineering

The detection rule successfully converted observed attack behavior into an actionable alert.

Detection:

```text
event.action : "authentication_failure"
and process.name : "sshd"
and host.name : "soc-linux"
```

Aggregation:

```text
source.ip
```

Threshold:

```text
>= 3
```

The controlled validation generated:

```text
4 matching events
```

and produced the expected Elastic alert.

---

# 3. Detection Validation

The project included a separate attack execution to validate the detection.

The controlled Hydra run reported:

```text
0 valid password found
```

Elastic subsequently generated the detection alert.

This demonstrated:

```text
Attack
↓
Endpoint Event
↓
Elastic Ingestion
↓
Detection Rule
↓
Alert
```

The detection was therefore validated against a real controlled attack rather than only being configured and assumed to work.

---

# 4. Investigation Lessons

## 4.1 Do Not Rely Only on the Alert

The alert established that the detection threshold had been reached.

The original authentication events were then examined in Discover.

This provided additional context:

```text
source.ip
user.name
event.action
process.name
host.name
```

The investigation therefore moved from:

```text
Alert
```

to:

```text
Underlying Events
```

before reaching a final finding.

---

# 5. Timezone Correlation

One of the most important investigation lessons was the difference between endpoint and Kibana time displays.

`soc-linux` uses:

```text
UTC
```

while the investigation interface was viewed in:

```text
IST
```

The attack therefore required explicit timezone correlation.

Example:

```text
03:27:17 UTC
=
08:57:17 IST
```

The corrected timeline allowed the following sources to be aligned:

```text
Kali
+
Linux SSH journal
+
Elastic events
+
Elastic alert
```

---

# 6. Evidence Correlation

The investigation became stronger when multiple evidence sources were correlated.

```text
Kali
│
├── Hydra attack output
│
└── 0 valid password found
        │
        ▼
Linux
│
├── sshd
├── authentication failures
└── source 192.168.1.10
        │
        ▼
Elastic
│
├── authentication_failure
├── source.ip
├── user.name
├── process.name
└── host.name
        │
        ▼
Detection
│
└── 4 matching events
        │
        ▼
Alert
│
└── Medium / Risk 47
```

This created a stronger evidence chain than relying on a single log source.

---

# 7. Successful Compromise Must Be Proven

The project title describes:

```text
SSH Brute Force → Compromise
```

However, the actual controlled run demonstrated:

```text
0 valid password found
```

Therefore:

```text
Password guessing:
Observed

Successful credential compromise:
Not demonstrated

Successful SSH access:
Not demonstrated

Host compromise:
Not demonstrated
```

This is an important SOC reporting lesson:

> Do not claim compromise when the collected evidence only demonstrates attempted access.

---

# 8. MITRE ATT&CK Mapping Lesson

The strongest observed ATT&CK mapping was:

```text
T1110.001
Brute Force: Password Guessing
```

SSH provided supporting context:

```text
T1021.004
Remote Services: SSH
```

The investigation did not provide evidence sufficient to map:

```text
T1078
Valid Accounts
```

because successful use of valid credentials was not demonstrated.

The lesson is to map ATT&CK techniques from observed evidence rather than automatically mapping every technique associated with a scenario.

---

# 9. Cyber Kill Chain Lesson

The attack progressed through the early stages:

```text
Reconnaissance
↓
Weaponization
↓
Delivery
↓
Exploitation
```

The attack did not demonstrate:

```text
Installation
Command & Control
Actions on Objectives
```

The project therefore demonstrated the importance of stopping the Kill Chain mapping where the evidence stops.

---

# 10. Detection Engineering Lessons

The project reinforced the difference between a hunting query and a production-style detection.

The process was:

```text
Observed Behavior
↓
Identify Reliable Fields
↓
Threat Hunt
↓
Review Events
↓
Reduce False Positives
↓
Create Detection
↓
Test Detection
↓
Generate Alert
↓
Investigate Alert
```

The detection was not considered complete merely because the rule was created.

It was validated using a controlled attack.

---

# 11. Threshold Detection Lesson

The rule used:

```text
Group by:
source.ip

Threshold:
>= 3
```

The attack generated:

```text
4 matching events
```

This provided a simple volume-based detection for repeated SSH authentication failures.

Future projects should evaluate whether:

```text
source IP
+
username
+
target host
+
time window
+
authentication outcome
```

provide better detection context for more complex attack scenarios.

---

# 12. False-Positive Lessons

Repeated authentication failures can occur during legitimate activity.

Potential legitimate causes include:

```text
Administrator mistakes
Automation
Monitoring systems
Password changes
Authorized security testing
```

Therefore, future detection engineering should consider:

```text
Source reputation
Expected administrative sources
Failure volume
Number of targeted accounts
Time of activity
Failure → success sequence
```

The goal is to improve detection fidelity without losing visibility.

---

# 13. Investigation Lessons

The investigation demonstrated the importance of answering:

```text
Who?
What?
Where?
When?
How?
Result?
```

For Project 01:

```text
Who:
socadmin

What:
Repeated SSH authentication failures

Where:
soc-linux / 192.168.1.16

Source:
192.168.1.10

When:
08:57:17–08:57:26 IST

How:
SSH password guessing

Result:
0 valid password found
```

---

# 14. Evidence Lessons

Evidence should be collected while the investigation is active.

Important evidence categories included:

```text
Attack evidence
Telemetry evidence
Hunting evidence
Detection evidence
Alert evidence
Investigation evidence
Timeline evidence
MITRE mapping
Incident response documentation
```

Screenshots should capture meaningful investigation states rather than only showing that a page exists.

---

# 15. Evidence Integrity

The project established a separation between:

```text
Raw Evidence
Sanitized Evidence
Published Evidence
```

Sensitive information should not be published to GitHub.

Do not commit:

```text
Passwords
Private keys
API tokens
Authentication secrets
Session tokens
Unnecessary personal information
```

---

# 16. Incident Response Lessons

The project showed that containment and remediation should be separated from investigation.

The workflow was:

```text
Detect
↓
Validate
↓
Investigate
↓
Determine Impact
↓
Contain
↓
Remediate
↓
Validate
```

Because this was an authorized lab simulation and no successful compromise was demonstrated:

```text
Production-style isolation:
Not performed

Credential reset:
Not performed

SSH blocking:
Not performed

Endpoint isolation:
Not performed
```

These actions were documented as recommendations rather than falsely reported as completed.

---

# 17. Timeline Lessons

A reliable timeline should combine multiple sources.

Project 01 used:

```text
Kali attack timestamp
+
Linux UTC journal
+
Elastic event timestamp
+
Elastic alert timestamp
```

The timeline demonstrated:

```text
08:57:17 IST
Attack begins

08:57:17–08:57:26 IST
SSH authentication failures

08:57:26 IST
Hydra completes
0 valid passwords

08:58:02 IST
Elastic alert generated
```

The earlier test run was excluded from the final investigation window.

---

# 18. Portfolio Lessons

Project 01 demonstrated that a strong SOC portfolio project should contain more than an attack screenshot.

The complete project includes:

```text
Baseline
Attack
Telemetry
Threat Hunting
Detection
Detection Logic
Investigation
Findings
Timeline
MITRE ATT&CK
Cyber Kill Chain
Incident Response
Containment
Remediation
Lessons Learned
Evidence
Screenshots
Diagrams
```

This creates an end-to-end SOC investigation rather than a standalone penetration-testing demonstration.

---

# 19. Improvements for Project 02

Project 02 should build on the Project 01 workflow.

Recommended improvements:

```text
1. Capture baseline evidence before attack
2. Verify timezone before attack
3. Record exact attack start time
4. Record exact attack end time
5. Use a dedicated investigation time window
6. Capture endpoint telemetry during the attack
7. Validate fields before writing KQL
8. Separate hunting queries from detection queries
9. Test detection with a controlled attack
10. Capture alert evidence
11. Correlate endpoint and SIEM evidence
12. Document unsuccessful attack outcomes honestly
```

---

# 20. SOC Workflow Template for Future Projects

Every future CatchMe project should follow:

```text
00. Baseline
        ↓
01. Attack Simulation
        ↓
02. Telemetry Validation
        ↓
03. Threat Hunting
        ↓
04. Detection Engineering
        ↓
05. Alert Investigation
        ↓
06. MITRE ATT&CK
        ↓
07. Cyber Kill Chain
        ↓
08. Incident Response
        ↓
09. Containment
        ↓
10. Remediation
        ↓
11. Evidence Preservation
        ↓
12. Lessons Learned
        ↓
13. GitHub Documentation
```

---

# 21. Final Project 01 Lessons

## What Was Demonstrated

```text
✓ Linux SSH attack simulation
✓ Real endpoint telemetry
✓ Elastic ingestion
✓ KQL threat hunting
✓ Detection engineering
✓ Threshold detection
✓ Alert generation
✓ Alert investigation
✓ Timeline reconstruction
✓ MITRE ATT&CK mapping
✓ Cyber Kill Chain mapping
✓ Incident response workflow
✓ Containment assessment
✓ Remediation planning
✓ Evidence preservation
```

## What Was Not Demonstrated

```text
✗ Successful password compromise
✗ Successful SSH login
✗ Privilege escalation
✗ Persistence
✗ Command & Control
✗ Data access
✗ Exfiltration
✗ Host compromise
```

---

# 22. Final Assessment

Project 01 successfully demonstrated an end-to-end SOC workflow for detecting and investigating controlled SSH password-guessing activity.

The most important lessons were:

```text
Evidence before assumptions
        ↓
Baseline before attack
        ↓
Validate telemetry
        ↓
Hunt before detecting
        ↓
Test the detection
        ↓
Correlate multiple sources
        ↓
Build an accurate timeline
        ↓
Map only observed behavior
        ↓
Do not overstate compromise
        ↓
Document containment and remediation separately
```

The Project 01 workflow is now suitable as the baseline methodology for subsequent CatchMe Linux SOC projects.

---

# 23. Project 01 Final Status

| Component             | Status   |
| --------------------- | -------- |
| Baseline              | Complete |
| Attack Simulation     | Complete |
| Telemetry Validation  | Complete |
| Threat Hunting        | Complete |
| Detection Engineering | Complete |
| Alert Generation      | Complete |
| Investigation         | Complete |
| Findings              | Complete |
| Timeline              | Complete |
| MITRE ATT&CK          | Complete |
| Cyber Kill Chain      | Complete |
| Incident Report       | Complete |
| Containment Plan      | Complete |
| Remediation Plan      | Complete |
| Lessons Learned       | Complete |

```text
PROJECT 01
SSH BRUTE FORCE DETECTION

STATUS: COMPLETE
```
