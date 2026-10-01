# Project 01 — Detection Logic

## 1. Purpose

This document explains the logical construction of the Project 01 SSH brute-force detection.

The purpose is to show how the observed attack behavior was converted into a repeatable detection condition.

---

## 2. Detection Objective

Detect repeated SSH authentication failures against the Linux SOC endpoint when multiple failures originate from the same source IP.

```text
SSH Authentication Failure
            ↓
       Identify SSH
            ↓
     Identify Target
            ↓
     Group by Source IP
            ↓
      Count Events
            ↓
       Threshold ≥ 3
            ↓
       Generate Alert
```
---

# 3. Observed Behavior

The controlled attack established the following telemetry pattern:

```text
source.ip     = 192.168.1.10
user.name     = socadmin
event.action  = authentication_failure
process.name  = sshd
host.name     = soc-linux
```

The behavior was repeatedly observed during the controlled Hydra test.

---

# 4. Detection Query

The detection query is:

```kql
event.action : "authentication_failure" and process.name : "sshd" and host.name : "soc-linux"
```

The query contains three conditions.

### Condition 1 — Authentication Failure

```kql
event.action : "authentication_failure"
```

Purpose:

Identify failed authentication events.

---

### Condition 2 — SSH Process

```kql
process.name : "sshd"
```

Purpose:

Restrict the detection to authentication activity associated with the Linux SSH daemon.

---

### Condition 3 — Target Host

```kql
host.name : "soc-linux"
```

Purpose:

Restrict the Project 01 detection to the controlled Linux SOC endpoint.

---

# 5. Combined Detection Logic

```text
event.action = authentication_failure
                    AND
process.name = sshd
                    AND
host.name = soc-linux
```

Only events satisfying all three conditions enter the threshold calculation.

---

# 6. Aggregation Logic

The matching events are grouped by:

```text
source.ip
```

This creates a separate event count for each source.

Example:

```text
192.168.1.10
      ↓
Authentication Failure
      ↓
Authentication Failure
      ↓
Authentication Failure
      ↓
Authentication Failure
      ↓
Count = 4
```

---

# 7. Threshold Logic

The configured threshold is:

```text
>= 3 events
```

Therefore:

```text
Count < 3
    ↓
No threshold alert

Count >= 3
    ↓
Threshold condition satisfied
    ↓
Generate alert
```

During validation:

```text
Observed events = 4
Required events  = 3
```

Therefore:

```text
4 >= 3
```

The detection condition was satisfied.

---

# 8. Complete Detection Model

```text
                    ┌─────────────────────┐
                    │   Linux SSH Event   │
                    └──────────┬──────────┘
                               ↓
                  ┌────────────────────────┐
                  │ authentication_failure│
                  └───────────┬────────────┘
                              ↓
                    ┌──────────────────┐
                    │ process = sshd   │
                    └────────┬─────────┘
                             ↓
                  ┌────────────────────┐
                  │ host = soc-linux   │
                  └─────────┬──────────┘
                            ↓
                   ┌─────────────────┐
                   │ Group source.ip│
                   └────────┬────────┘
                            ↓
                   ┌─────────────────┐
                   │ Count matching  │
                   │     events      │
                   └────────┬────────┘
                            ↓
                      ┌───────────┐
                      │   >= 3?   │
                      └─────┬─────┘
                            │
                 ┌──────────┴──────────┐
                 │                     │
                YES                    NO
                 ↓                     ↓
        ┌────────────────┐       No Alert
        │ Generate Alert │
        └────────────────┘
```

---

# 9. Detection Input Fields

| Field          | Value / Role                      |
| -------------- | --------------------------------- |
| `event.action` | Identifies authentication failure |
| `process.name` | Identifies `sshd`                 |
| `host.name`    | Identifies `soc-linux`            |
| `source.ip`    | Aggregation key                   |
| `user.name`    | Identifies targeted account       |
| `@timestamp`   | Event timing                      |

---

# 10. Source IP Aggregation

The source IP is the primary aggregation key.

```text
Source IP
    ↓
192.168.1.10
    ↓
Count matching SSH authentication failures
    ↓
4 events
    ↓
Threshold ≥ 3
    ↓
Alert
```

This allows repeated activity from the same source to be identified as a behavioral pattern.

---

# 11. Detection Example

### Event 1

```text
source.ip = 192.168.1.10
event.action = authentication_failure
```

Count:

```text
1
```

No alert.

---

### Event 2

```text
source.ip = 192.168.1.10
event.action = authentication_failure
```

Count:

```text
2
```

No alert.

---

### Event 3

```text
source.ip = 192.168.1.10
event.action = authentication_failure
```

Count:

```text
3
```

Threshold reached.

```text
ALERT
```

---

### Event 4

```text
source.ip = 192.168.1.10
event.action = authentication_failure
```

Count:

```text
4
```

The Project 01 validation produced four matching events.

---

# 12. Detection Validation

The detection was tested using the controlled SSH authentication attack.

### Attacker

```text
Kali
192.168.1.10
```

### Target

```text
soc-linux
192.168.1.16
```

### Service

```text
SSH
TCP/22
```

### Account

```text
socadmin
```

### Attack Result

```text
0 valid password found
```

### Elastic Result

```text
4 matching authentication_failure events
```

### Detection Result

```text
Medium severity alert
Risk score: 47
```

---

# 13. Detection Decision Flow

```text
                    SSH Event
                       ↓
             Is authentication failed?
                       ↓
                      YES
                       ↓
                Is process sshd?
                       ↓
                      YES
                       ↓
             Is host soc-linux?
                       ↓
                      YES
                       ↓
             Group by source.ip
                       ↓
             Count matching events
                       ↓
                 Count >= 3?
                  ↙          ↘
                YES           NO
                 ↓             ↓
             ALERT          No Alert
```

---

# 14. False-Positive Logic

The detection is not intended to classify every failed SSH login as malicious.

Examples of legitimate failures include:

```text
Incorrect administrator password
        ↓
Single authentication failure
        ↓
No threshold alert
```

Repeated failures:

```text
Multiple failures
        ↓
Same source IP
        ↓
Same SSH service
        ↓
Threshold reached
        ↓
Alert
```

The alert should therefore be treated as an investigation trigger rather than proof of compromise.

---

# 15. Detection Limitations

The detection does not establish:

```text
Successful authentication
Successful compromise
Privilege escalation
Persistence
Lateral movement
Command execution
Data access
```

It establishes:

```text
Repeated SSH authentication failures
```

Additional investigation is required to determine whether the activity progressed beyond authentication failures.

---

# 16. Detection-to-Investigation Handoff

When the rule generates an alert, the SOC investigation should identify:

```text
Alert
  ↓
Source IP
  ↓
Target Host
  ↓
Target User
  ↓
Authentication Events
  ↓
SSH Process
  ↓
Timeline
  ↓
Successful Authentication Check
  ↓
Post-Authentication Activity Check
  ↓
MITRE ATT&CK Mapping
  ↓
Incident Classification
```

The investigation workflow follows the project's documented approach of validating suspiciousness, identifying host/user/process information, building a timeline, correlating events, mapping relevant ATT&CK behavior, and classifying the finding.

---

# 17. Project 01 Detection Logic Summary

```text
QUERY

event.action : "authentication_failure"
AND process.name : "sshd"
AND host.name : "soc-linux"


GROUP BY

source.ip


THRESHOLD

>= 3


RESULT

Medium severity alert
Risk score 47
```

---

# 18. Detection Evidence

### Rule Configuration

```text
screenshots/detection/01-threshold-rule-configuration.png
screenshots/detection/01-threshold-rule-schedule.png
```

### Rule State

```text
screenshots/detection/03-rule-created-enabled.png
```

### Attack Validation

```text
screenshots/attack/02-kali-hydra-detection-test.png
```

### Alert

```text
screenshots/detection/05-brute-force-alert-generated.png
```

### Investigation

```text
screenshots/investigation/01-alert-overview.png
screenshots/investigation/02-alert-table-metadata.png
screenshots/investigation/04-source-events-correlation.png
```

---

# 19. Final Detection Logic

```text
┌───────────────────────────────┐
│ SSH Authentication Failure    │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│ process.name = sshd           │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│ host.name = soc-linux         │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│ Group by source.ip            │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│ Count authentication failures │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│ Threshold >= 3                │
└───────────────┬───────────────┘
                ↓
┌───────────────────────────────┐
│ Elastic Detection Alert       │
└───────────────────────────────┘
```

---

# 20. Detection Engineering Status

```text
[✓] Behavior identified
[✓] Required fields identified
[✓] KQL validated
[✓] Aggregation field selected
[✓] Threshold defined
[✓] False-positive considerations documented
[✓] Rule created
[✓] Rule enabled
[✓] Controlled attack executed
[✓] Alert generated
[✓] Alert investigated
[✓] Evidence captured
```


