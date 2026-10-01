# Project 01 — Detection Engineering

## 1. Purpose

This document records the detection-engineering process used to convert the observed SSH password-guessing behavior into an Elastic SIEM detection.

The detection was designed from the behavior identified during threat hunting and was validated using a controlled attack against the CatchMe Linux SOC lab.

---

## 2. Detection Objective

Detect repeated SSH authentication failures against `soc-linux` originating from the same source IP.

### Detection Objective

```text
Repeated SSH Authentication Failures
                    ↓
             Same Source IP
                    ↓
             Threshold ≥ 3
                    ↓
             Generate Alert
```
The detection is specifically intended for the controlled CatchMe Linux SOC environment.

---

# 3. Observed Attack Behavior

The threat hunt established the following behavior:

```text
source.ip     = 192.168.1.10
user.name     = socadmin
event.action  = authentication_failure
process.name  = sshd
host.name     = soc-linux
```

The controlled attack generated four matching authentication-failure events during the detection-test window.

---

# 4. Detection Query

The final detection query was:

```kql
event.action : "authentication_failure" and process.name : "sshd" and host.name : "soc-linux"
```

### Query Logic

```text
event.action = authentication_failure
        AND
process.name = sshd
        AND
host.name = soc-linux
```

The query restricts the detection to:

* Authentication failures
* SSH daemon activity
* The Project 01 Linux endpoint

---

# 5. Aggregation Logic

The detection uses a **Threshold** rule.

### Grouping Field

```text
source.ip
```

### Threshold

```text
>= 3
```

### Logic

```text
Matching SSH authentication failures
              ↓
          Group by
          source.ip
              ↓
       Count events
              ↓
          >= 3
              ↓
       Generate alert
```

This allows repeated failures from the same source to be detected as a behavioral pattern rather than treating every authentication failure as an individual incident.

---

# 6. Detection Rule

| Setting              | Value                                       |
| -------------------- | ------------------------------------------- |
| Rule name            | `CatchMe - Linux SSH Brute Force Detection` |
| Rule type            | Threshold                                   |
| Severity             | Medium                                      |
| Risk score           | 47                                          |
| Schedule             | 1 minute                                    |
| Additional look-back | 1 minute                                    |
| Group by             | `source.ip`                                 |
| Threshold            | `>= 3`                                      |
| Suppression          | Off                                         |
| Actions              | None                                        |
| Status               | Enabled                                     |

---

# 7. Rule Tags

The rule was created with the following tags:

```text
CatchMe
Linux
SSH
Brute-Force
T1110
Project-01
```

These tags provide project and investigation context when reviewing the detection in Elastic.

---

# 8. Detection Index Patterns

The rule used the configured Elastic detection index patterns:

```text
apm-*-transaction*
auditbeat-*
endgame-*
filebeat-*
logs-*
packetbeat-*
traces-apm*
winlogbeat-*
*-elastic-cloud-logs-*
```

The actual Project 01 SSH telemetry was available through the Elastic log data used by the detection rule.

---

# 9. Detection Schedule

The rule was configured with:

```text
Schedule:
1 minute

Additional look-back:
1 minute
```

This allows the detection engine to periodically evaluate the recent event window.

---

# 10. Initial Rule Validation

Before executing the detection-validation attack:

```text
Rule status:
Enabled

Last response:
Succeeded

Alerts:
None
```

This established the initial state before the controlled attack test.

---

# 11. Controlled Detection Test

The same controlled Hydra SSH test was executed from the Kali attacker system.

Target:

```text
192.168.1.16:22
```

Source:

```text
192.168.1.10
```

Target account:

```text
socadmin
```

The test used the controlled password list created for Project 01.

Hydra reported:

```text
0 valid password found
```

Therefore, the detection test generated authentication-failure telemetry without demonstrating successful authentication.

---

# 12. Detection Result

After the controlled attack, Elastic generated an alert.

### Alert

```text
Rule:
CatchMe - Linux SSH Brute Force Detection

Severity:
Medium

Risk score:
47

Status:
Open
```

### Threshold Result

```text
Count:
4

Source:
192.168.1.10
```

The alert reason identified:

```text
source 192.168.1.10
```

as the source associated with the threshold condition.

---

# 13. Detection Timeline

```text
08:57:17 IST
      ↓
Hydra starts
      ↓
SSH authentication failures
      ↓
08:57:17–08:57:26
Multiple authentication failures
      ↓
08:57:26
Hydra finishes
      ↓
0 valid passwords
      ↓
08:57:26.518
Elastic original event time
      ↓
08:58:02.247
Detection alert generated
```

The endpoint uses UTC while Kali uses IST, so the endpoint timeline was normalized before correlation.

---

# 14. Detection Validation

The detection was validated against the following evidence:

### Attacker Evidence

```text
screenshots/attack/02-kali-hydra-detection-test.png
```

### Detection Configuration

```text
screenshots/detection/01-threshold-rule-configuration.png
screenshots/detection/01-threshold-rule-schedule.png
screenshots/detection/02-rule-actions.png
screenshots/detection/03-rule-created-enabled.png
```

### Alert Generation

```text
screenshots/detection/05-brute-force-alert-generated.png
```

### Investigation

```text
screenshots/investigation/01-alert-overview.png
screenshots/investigation/02-alert-table-metadata.png
screenshots/investigation/04-source-events-correlation.png
screenshots/investigation/05-linux-ssh-journal-current-attack.png
```

---

# 15. Detection-to-Telemetry Correlation

The detection can be represented as:

```text
Kali
192.168.1.10
      │
      │ SSH authentication attempts
      ▼
soc-linux
192.168.1.16
      │
      │ sshd
      ▼
Authentication Failure
      │
      │ Elastic Agent
      ▼
Elastic SIEM
      │
      │ KQL
      ▼
authentication_failure
      │
      │ Group by source.ip
      ▼
4 matching events
      │
      │ Threshold >= 3
      ▼
Medium Alert
Risk Score 47
```

---

# 16. Detection Logic

### Behavioral Logic

```text
IF

event.action = authentication_failure

AND

process.name = sshd

AND

host.name = soc-linux

AND

3 or more matching events

GROUPED BY

source.ip

THEN

generate detection alert
```

---

# 17. Why the Detection Triggered

The controlled attack produced:

```text
source.ip = 192.168.1.10
```

with:

```text
4 matching authentication_failure events
```

The configured threshold was:

```text
>= 3
```

Therefore:

```text
4 >= 3
```

The threshold condition was satisfied and Elastic generated the alert.

---

# 18. False-Positive Considerations

Potential legitimate authentication failures include:

* Incorrect administrator password
* User login mistakes
* Automated administration activity
* Misconfigured SSH clients
* Scheduled processes using outdated credentials

A single authentication failure is therefore insufficient to represent the Project 01 behavioral pattern.

The threshold reduces sensitivity to isolated failures by requiring repeated matching events from the same source IP.

---

# 19. Detection Limitations

This detection is intentionally scoped to the Project 01 laboratory endpoint.

It does not independently prove:

* Successful credential compromise
* Successful SSH login
* Privilege escalation
* Persistence
* Lateral movement
* Host compromise
* Malicious intent

The detection identifies repeated SSH authentication failures.

Further investigation is required to determine whether authentication eventually succeeds or whether post-authentication activity occurs.

---

# 20. Detection Improvement Opportunities

Future versions of the detection could correlate:

```text
Repeated Authentication Failures
              ↓
Successful Authentication
              ↓
Same Source IP
              ↓
Same User
              ↓
Short Time Window
              ↓
Higher Investigation Priority
```

Additional telemetry could also be correlated with:

* SSH successful-login events
* User identity
* Source IP reputation
* Process activity
* Command execution
* Privilege escalation
* Persistence
* Network connections

These improvements should be validated against baseline activity before deployment outside the lab.

---

# 21. Detection Engineering Evidence

The complete detection-engineering evidence chain is:

```text
Observed Attack
      ↓
Endpoint Telemetry
      ↓
Threat Hunt
      ↓
Reliable ECS Fields
      ↓
Detection Query
      ↓
Threshold Design
      ↓
Rule Configuration
      ↓
Baseline Validation
      ↓
Controlled Attack Test
      ↓
Alert Generated
      ↓
Alert Investigation
```

---

# 22. Detection Status

```text
[✓] Attack behavior identified
[✓] Telemetry validated
[✓] KQL query validated
[✓] Detection logic defined
[✓] Threshold selected
[✓] Rule created
[✓] Rule enabled
[✓] Initial state verified
[✓] Controlled attack executed
[✓] Threshold condition satisfied
[✓] Alert generated
[✓] Alert investigated
[✓] Detection evidence captured
```

---

# 23. Final Detection Result

**Detection:** Successful

**Observed behavior:** Repeated SSH authentication failures

**Source:** `192.168.1.10`

**Target:** `soc-linux` (`192.168.1.16`)

**Account:** `socadmin`

**Process:** `sshd`

**Matching events:** `4`

**Threshold:** `>= 3`

**Alert severity:** Medium

**Risk score:** 47

**Successful password:** Not observed

**Successful compromise:** Not demonstrated


```
