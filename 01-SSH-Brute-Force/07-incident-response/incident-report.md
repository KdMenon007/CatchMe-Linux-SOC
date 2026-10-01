# Incident Response Flow

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Incident ID:** `CATCHME-LNX-001`  
**Scenario:** SSH Brute Force Detection  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Service:** SSH (`TCP/22`)

---

# 1. Incident Response Objective

This document describes the SOC incident-response workflow used after the Project 01 SSH password-guessing activity generated an Elastic detection alert.

The workflow covers:

```text
Detection
    ↓
Validation
    ↓
Investigation
    ↓
Containment
    ↓
Remediation
    ↓
Validation
    ↓
Lessons Learned

```
Only actions actually performed during the lab are identified as completed.

Containment and remediation actions that were not executed are documented as recommendations.

---

# 2. Incident Summary

A controlled SSH password-guessing simulation was launched from:

```text
192.168.1.10
```

against:

```text
soc-linux
192.168.1.16
```

The attack targeted:

```text
socadmin
```

through:

```text
SSH
TCP/22
```

Linux SSH telemetry recorded repeated authentication failures.

Elastic SIEM detected the activity using the custom threshold rule:

```text
CatchMe - Linux SSH Brute Force Detection
```

The rule generated a:

```text
Medium
Risk Score: 47
```

alert.

The controlled attack reported:

```text
0 valid password found
```

No successful credential compromise was demonstrated.

---

# 3. Incident Response Workflow

```text
┌──────────────────────┐
│  Detection           │
│  Elastic Alert       │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  Validation          │
│  Confirm suspicious  │
│  authentication      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  Investigation       │
│  Host/User/IP/Time   │
│  Correlation         │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  Containment         │
│  Recommended actions │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  Remediation         │
│  Harden + tune       │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  Validation          │
│  Confirm controls    │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  Lessons Learned     │
└──────────────────────┘
```

---

# 4. Phase 1 — Detection

## Detection Source

The Elastic detection rule identified repeated SSH authentication failures.

```text
Rule:
CatchMe - Linux SSH Brute Force Detection
```

Detection query:

```kql
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

Observed detection result:

```text
Source IP: 192.168.1.10
Matching events: 4
Severity: Medium
Risk Score: 47
```

## Status

**Complete**

---

# 5. Phase 2 — Alert Validation

The alert was reviewed to determine whether the detection corresponded to actual endpoint activity.

The alert contained:

```text
kibana.alert.rule.type = threshold
kibana.alert.threshold_result.count = 4
kibana.alert.threshold_result.terms.value = 192.168.1.10
```

The original events were then reviewed in Elastic Discover.

Investigation query:

```kql
event.action : "authentication_failure"
and source.ip : "192.168.1.10"
and host.name : "soc-linux"
```

The investigation window was narrowed to:

```text
08:55 → 09:00 IST
```

Four current attack events were identified.

## Status

**Complete**

---

# 6. Phase 3 — Investigation

The investigation established the following:

| Investigation Element        | Finding                  |
| ---------------------------- | ------------------------ |
| Source IP                    | `192.168.1.10`           |
| Target IP                    | `192.168.1.16`           |
| Target hostname              | `soc-linux`              |
| Account                      | `socadmin`               |
| Service                      | SSH                      |
| Process                      | `sshd`                   |
| Event action                 | `authentication_failure` |
| Matching events              | 4                        |
| Attack tool                  | Hydra                    |
| Authentication result        | Failed                   |
| Successful password          | Not demonstrated         |
| Post-authentication activity | Not demonstrated         |

---

# 7. Timeline Correlation

The attack was correlated across Kali, Linux, and Elastic.

### Attack

```text
08:57:17 IST
```

Hydra started the controlled SSH password-guessing activity.

### Linux

```text
03:27:17 UTC
```

SSH authentication failures began.

Because `soc-linux` uses UTC:

```text
03:27:17 UTC
=
08:57:17 IST
```

### Elastic

The relevant authentication events were visible in the corresponding Elastic investigation window.

### Detection

The Elastic alert was generated at:

```text
08:58:02.247 IST
```

This established temporal correlation between:

```text
Hydra
  ↓
SSH
  ↓
Linux authentication failures
  ↓
Elastic telemetry
  ↓
Detection rule
  ↓
Alert
```

---

# 8. Phase 4 — Containment

## Actual Lab Action

No destructive containment action was performed against the lab endpoint.

The purpose of Project 01 was detection and investigation validation.

## Recommended Containment

In a production environment, the SOC could evaluate:

1. Confirm the source IP.
2. Determine whether the source is authorized.
3. Block or restrict the malicious source where appropriate.
4. Review the targeted account.
5. Check for successful authentication.
6. Review activity after any successful login.
7. Preserve relevant evidence.
8. Apply network-level containment where justified.
9. Temporarily restrict SSH exposure if required.
10. Escalate according to the organization's incident-response process.

These are recommendations and were **not executed as production containment actions** during Project 01.

## Status

**Recommended — Not Performed**

---

# 9. Phase 5 — Account Protection

Because the targeted account was:

```text
socadmin
```

the following checks are recommended:

```text
Authentication history
Successful SSH logins
Failed authentication volume
Unexpected source addresses
Password exposure
SSH key changes
Privilege changes
Recent account activity
```

The Project 01 attack itself did not demonstrate successful authentication.

---

# 10. Phase 6 — Remediation

Recommended remediation includes:

### SSH Hardening

```text
Prefer key-based authentication
Restrict SSH exposure
Restrict administrative SSH access
Review root SSH configuration
Review password authentication requirements
```

### Brute-Force Protection

```text
Rate limiting
Source restriction
Account protection
Authentication monitoring
Automated blocking where appropriate
```

### SIEM Improvements

```text
Monitor repeated authentication failures
Correlate failures followed by success
Monitor unusual SSH source IPs
Monitor privileged account authentication
Tune false positives
Maintain baseline behavior
```

No production SSH configuration was changed as part of this project.

## Status

**Recommended — Not Performed**

---

# 11. Phase 7 — Detection Improvement

The Project 01 detection successfully identified repeated failures from the same source.

A future improvement is to detect a sequence such as:

```text
Multiple authentication failures
        ↓
Successful authentication
        ↓
Same source IP
        ↓
Same account
        ↓
Short time window
        ↓
Higher-priority investigation
```

This would help distinguish:

```text
Repeated failed access
```

from:

```text
Potential successful compromise
```

---

# 12. Phase 8 — Evidence Preservation

Relevant evidence includes:

```text
Kali attack output
Linux SSH journal
Elastic authentication events
Elastic hunting results
Detection-rule configuration
Elastic alert
Investigation timeline
MITRE mapping
Cyber Kill Chain mapping
```

Evidence directories:

```text
evidence/
├── raw/
├── sanitized/
└── hashes/
```

Screenshots:

```text
screenshots/
├── attack/
├── telemetry/
├── hunting/
├── detection/
└── investigation/
```

Evidence should be sanitized before publication.

Sensitive credentials, secrets, tokens, private keys, and unrelated personal information must not be committed to GitHub.

---

# 13. Phase 9 — Incident Classification

Based on the observed evidence:

```text
Incident Type:
SSH password-guessing activity

ATT&CK:
T1110.001 — Password Guessing

Supporting Context:
T1021.004 — SSH

Detection:
Successful

Authentication:
Failed

Credential compromise:
Not demonstrated

Host compromise:
Not demonstrated
```

---

# 14. Phase 10 — Impact Assessment

## Observed Impact

```text
SSH authentication service targeted
Repeated authentication failures generated
SOC alert generated
SOC investigation performed
```

## Not Demonstrated

```text
Successful account compromise
Successful SSH session
Privilege escalation
Persistence
Malware installation
Data access
Data exfiltration
System modification
Destructive activity
```

Therefore, the collected Project 01 evidence does not demonstrate host compromise.

---

# 15. Incident Response Decision Flow

```text
Elastic Alert
     ↓
Is the alert based on real events?
     │
     ├── No → Close / Tune Detection
     │
     └── Yes
           ↓
Identify Source
           ↓
Identify Target
           ↓
Identify Account
           ↓
Check Authentication Result
           ↓
Successful Login?
     │
     ├── No
     │    ↓
     │  Treat as Failed Credential
     │  Access Attempt
     │
     └── Yes
          ↓
        Escalate
          ↓
        Investigate
          ↓
        Contain
```

For Project 01:

```text
Successful Login?
        ↓
       NO
        ↓
Failed Credential-Access Attempt
        ↓
Detection + Investigation
```

---

# 16. SOC Handoff

The alert investigation produces the following handoff information:

```text
Incident ID: CATCHME-LNX-001

Source:
192.168.1.10

Target:
soc-linux / 192.168.1.16

Account:
socadmin

Service:
SSH / TCP 22

Activity:
Repeated authentication failures

Detection:
CatchMe - Linux SSH Brute Force Detection

Severity:
Medium

Risk:
47

ATT&CK:
T1110.001

Authentication:
No successful credential demonstrated
```

---

# 17. Evidence Chain

```text
Attack
  ↓
Kali Hydra Output
  ↓
Linux SSH Journal
  ↓
Elastic Authentication Events
  ↓
KQL Hunt
  ↓
Detection Rule
  ↓
Elastic Alert
  ↓
Investigation
  ↓
Timeline
  ↓
MITRE ATT&CK
  ↓
Cyber Kill Chain
  ↓
Incident Response
```

---

# 18. Final Incident Response Status

| Response Stage              | Status        |
| --------------------------- | ------------- |
| Detection                   | Complete      |
| Alert validation            | Complete      |
| Investigation               | Complete      |
| Timeline correlation        | Complete      |
| Evidence collection         | Complete      |
| MITRE mapping               | Complete      |
| Kill Chain mapping          | Complete      |
| Containment assessment      | Complete      |
| Production containment      | Not performed |
| Remediation recommendations | Complete      |
| Production remediation      | Not performed |
| Lessons learned             | Complete      |

---

# 19. Final Assessment

Project 01 demonstrated a complete SOC investigation and incident-response workflow for a controlled SSH password-guessing attempt.

The detection successfully identified repeated authentication failures from the Kali attacker.

The investigation correlated:

```text
Source IP
+
Target Host
+
User
+
SSH Process
+
Authentication Failure
+
Timestamp
```

The available evidence demonstrates a **failed SSH password-guessing attempt that was successfully detected and investigated**.

No successful credential compromise or host compromise was demonstrated.

---

# 20. Project 01 Response Status

```text
Detection       ✓
Validation      ✓
Investigation   ✓
MITRE Mapping   ✓
Kill Chain      ✓
Containment     ✓ Assessment
Remediation     ✓ Recommendations
Evidence        ✓
Reporting       ✓
```
