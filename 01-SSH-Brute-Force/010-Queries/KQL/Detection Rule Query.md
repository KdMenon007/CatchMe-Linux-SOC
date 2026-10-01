# Detection Rule Query

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Incident ID:** `CATCHME-LNX-001`  
**Scenario:** SSH Brute Force Detection  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Service:** SSH (`TCP/22`)

---

# 1. Query Objective

This document describes the KQL query used as the detection condition for the Project 01 Elastic Threshold Rule.

The query was designed to identify SSH authentication failures on the Linux SOC endpoint and provide the event set used for threshold-based brute-force detection.

---

# 2. Detection Query

```kql
event.action : "authentication_failure" and process.name : "sshd" and host.name : "soc-linux"
```
---

# 3. Query Breakdown

### Authentication Failure

```kql
event.action : "authentication_failure"
```

Identifies authentication-failure events.

### SSH Process

```kql
process.name : "sshd"
```

Restricts the detection to events associated with the SSH daemon.

### Target Host

```kql
host.name : "soc-linux"
```

Restricts the detection to the Linux SOC endpoint.

---

# 4. Detection Logic

The query provides the event conditions, while the Threshold rule evaluates the volume of matching events.

```text
┌──────────────────────────────┐
│ SSH Authentication Failure  │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Process = sshd               │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Host = soc-linux             │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Group Events by source.ip    │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Threshold >= 3               │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ SSH Brute Force Alert        │
└──────────────────────────────┘
```

---

# 5. Detection Rule Configuration

| Setting              | Configuration                                     |
| -------------------- | ------------------------------------------------- |
| Rule Name            | `CatchMe - Linux SSH Brute Force Detection`       |
| Rule Type            | Threshold                                         |
| Query                | SSH authentication failure + `sshd` + `soc-linux` |
| Group By             | `source.ip`                                       |
| Threshold            | `>= 3`                                            |
| Severity             | Medium                                            |
| Risk Score           | 47                                                |
| Schedule             | 1 minute                                          |
| Additional Look-back | 1 minute                                          |
| Suppression          | Off                                               |

---

# 6. Detection Validation

The detection rule was tested using the controlled SSH password-guessing simulation from Kali.

The attack generated repeated authentication failures against `soc-linux`.

The threshold evaluation identified:

```text
Source IP        : 192.168.1.10
Matching Events  : 4
Threshold        : >= 3
```

The rule subsequently generated an Elastic alert.

---

# 7. Alert Correlation

```text
Kali
192.168.1.10
     ↓
SSH Password Guessing
     ↓
Authentication Failures
     ↓
sshd
     ↓
Elastic Agent
     ↓
Elastic SIEM
     ↓
KQL Detection Query
     ↓
Threshold >= 3
     ↓
CatchMe Detection Alert
```

---

# 8. Detection Result

The detection successfully generated an alert with:

```text
Rule       : CatchMe - Linux SSH Brute Force Detection
Severity   : Medium
Risk Score : 47
Source IP  : 192.168.1.10
Count      : 4
Status     : Open
```

The alert provided the starting point for the subsequent SOC investigation.

---

# 9. Evidence

Related detection evidence is available under:

```text
screenshots/
└── detection/
    ├── 01-threshold-rule-configuration.png
    ├── 01-threshold-rule-schedule.png
    ├── 03-rule-created-enabled.png
    └── 05-brute-force-alert-generated.png
```

