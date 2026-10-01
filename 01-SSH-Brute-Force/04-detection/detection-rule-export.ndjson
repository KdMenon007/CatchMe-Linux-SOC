# Project 01 — Investigation

## 1. Purpose

This document records the SOC investigation performed after the Project 01 SSH brute-force detection alert was generated.

The investigation validates the alert against:

- Elastic SIEM telemetry
- Linux SSH journal evidence
- Kali attack evidence
- Source IP
- Target host
- Target account
- Process
- Timeline
- Authentication outcome

The objective is to determine what occurred, whether authentication succeeded, and whether the evidence supports a compromise.

---

# 2. Investigation Overview

```text
Detection Alert
      ↓
Validate Alert
      ↓
Identify Source
      ↓
Identify Target
      ↓
Identify User
      ↓
Review Authentication Events
      ↓
Correlate Endpoint SSH Logs
      ↓
Correlate Attack Timeline
      ↓
Check Successful Authentication
      ↓
Determine Impact
      ↓
MITRE ATT&CK Mapping
      ↓
Incident Classification
```
---

# 3. Alert Under Investigation

| Attribute       | Value                                       |
| --------------- | ------------------------------------------- |
| Rule            | `CatchMe - Linux SSH Brute Force Detection` |
| Rule type       | Threshold                                   |
| Severity        | Medium                                      |
| Risk score      | 47                                          |
| Status          | Open                                        |
| Source IP       | `192.168.1.10`                              |
| Threshold count | 4                                           |
| Threshold       | `>= 3`                                      |
| Target host     | `soc-linux`                                 |
| Target IP       | `192.168.1.16`                              |

---

# 4. Initial Alert Validation

The alert was generated after the controlled Hydra SSH test.

The alert reason identified:

```text
event with source 192.168.1.10 created medium alert CatchMe - Linux SSH Brute Force Detection
```

The alert showed:

```text
kibana.alert.rule.type = threshold
kibana.alert.threshold_result.count = 4
kibana.alert.threshold_result.terms.value = 192.168.1.10
```

### Initial Assessment

The alert is consistent with the detection logic because:

```text
Observed matching events = 4
Configured threshold     = 3
```

Therefore:

```text
4 >= 3
```

The detection condition was satisfied.

---

# 5. Source Identification

The alert identified:

```text
source.ip = 192.168.1.10
```

The lab mapping identifies:

```text
192.168.1.10 = Kali attacker
```

Therefore the suspicious authentication activity originated from the controlled Kali attack system.

---

# 6. Target Identification

The detection was restricted to:

```text
host.name = soc-linux
```

The target endpoint is:

```text
Hostname:
soc-linux

IP:
192.168.1.16
```

The targeted service was:

```text
SSH
TCP/22
```

---

# 7. Target Account Identification

The correlated Elastic events identified:

```text
user.name = socadmin
```

Therefore the authentication activity targeted:

```text
socadmin
```

on:

```text
soc-linux
```

---

# 8. Process Identification

The correlated events showed:

```text
process.name = sshd
```

This is consistent with authentication activity against the Linux SSH service.

The investigation therefore established:

```text
Kali
192.168.1.10
      ↓
SSH
TCP/22
      ↓
sshd
      ↓
soc-linux
192.168.1.16
      ↓
socadmin
```

---

# 9. Elastic Investigation Query

The investigation used the following KQL:

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```

The investigation time range was restricted to:

```text
08:55–09:00 IST
```

This isolated the current detection-validation attack and excluded the earlier Project 01 test run.

---

# 10. Correlated Elastic Events

The isolated investigation window returned:

```text
4 authentication_failure events
```

The observed fields were:

```text
source.ip     = 192.168.1.10
user.name     = socadmin
event.action  = authentication_failure
process.name  = sshd
host.name     = soc-linux
```

### Event Timeline

| Time (IST)   | Source IP      | User       | Action                   | Process | Host        |
| ------------ | -------------- | ---------- | ------------------------ | ------- | ----------- |
| 08:57:17.887 | `192.168.1.10` | `socadmin` | `authentication_failure` | `sshd`  | `soc-linux` |
| 08:57:17.888 | `192.168.1.10` | `socadmin` | `authentication_failure` | `sshd`  | `soc-linux` |
| 08:57:23.642 | `192.168.1.10` | `socadmin` | `authentication_failure` | `sshd`  | `soc-linux` |
| 08:57:26.518 | `192.168.1.10` | `socadmin` | `authentication_failure` | `sshd`  | `soc-linux` |

---

# 11. Endpoint Evidence Correlation

The Elastic events were validated against the Linux SSH journal.

The endpoint recorded:

```text
pam_unix(sshd:auth): authentication failure
```

from:

```text
192.168.1.10
```

against:

```text
user=socadmin
```

The SSH journal also recorded:

```text
Failed password for socadmin from 192.168.1.10
```

and subsequent connection closures during pre-authentication.

This independently supports the authentication-failure telemetry observed in Elastic.

---

# 12. Attack Evidence Correlation

The Kali attack was executed against:

```text
192.168.1.16:22
```

The Hydra output reported:

```text
1 of 1 target completed, 0 valid password found
```

Therefore the attack evidence does not demonstrate a successful password authentication.

---

# 13. Timezone Correlation

The two systems use different time zones.

### Kali

```text
Asia/Kolkata
UTC+05:30
```

### soc-linux

```text
Etc/UTC
UTC+00:00
```

Therefore:

```text
03:27:17 UTC = 08:57:17 IST
03:27:26 UTC = 08:57:26 IST
```

This confirms that the endpoint SSH activity aligns with the Kali attack window.

---

# 14. Investigation Timeline

```text
08:57:17 IST
    ↓
Hydra starts SSH authentication test
    ↓
08:57:17
SSH authentication failures begin
    ↓
08:57:19
Failed password attempts
    ↓
08:57:22
Additional failed password attempts
    ↓
08:57:23
Authentication failure observed in Elastic
    ↓
08:57:25
Final failed password activity
    ↓
08:57:26
SSH connection closes
    ↓
08:57:26
Hydra reports 0 valid passwords
    ↓
08:57:26.518
Elastic original event
    ↓
08:58:02.247
Detection alert generated
```

---

# 15. Authentication Outcome

The investigation specifically checked whether the authentication attack resulted in a successful login.

### Evidence

Hydra reported:

```text
0 valid password found
```

Elastic investigation events showed:

```text
event.action = authentication_failure
```

Linux SSH logs showed:

```text
Failed password
```

and:

```text
[preauth]
```

### Finding

```text
Successful password authentication:
Not observed
```

---

# 16. Successful Login Check

The available evidence does not demonstrate a successful SSH login resulting from the controlled brute-force test.

The observed activity remained within the authentication/pre-authentication phase.

Therefore:

```text
Successful SSH compromise:
Not demonstrated
```

---

# 17. Post-Authentication Activity

No post-authentication activity was demonstrated as part of this attack.

The investigation therefore does not claim:

```text
Command execution
Privilege escalation
Persistence
Lateral movement
Data access
Host compromise
```

These would require additional evidence.

---

# 18. Evidence Correlation Matrix

| Evidence Source   | Observation                          | Correlation                         |
| ----------------- | ------------------------------------ | ----------------------------------- |
| Kali              | Hydra SSH test                       | Attack source                       |
| Kali              | `0 valid password found`             | No successful password demonstrated |
| Linux SSH journal | Authentication failures              | Endpoint validation                 |
| Linux SSH journal | Failed passwords from `192.168.1.10` | Source correlation                  |
| Elastic Discover  | 4 `authentication_failure` events    | SIEM validation                     |
| Elastic Discover  | `source.ip = 192.168.1.10`           | Attacker correlation                |
| Elastic Discover  | `user.name = socadmin`               | Target account                      |
| Elastic Discover  | `process.name = sshd`                | Service correlation                 |
| Elastic Alert     | Threshold count `4`                  | Detection validation                |
| Elastic Alert     | Source `192.168.1.10`                | Alert correlation                   |

---

# 19. Evidence Chain

```text
Kali Attack
    ↓
192.168.1.10
    ↓
SSH TCP/22
    ↓
soc-linux
192.168.1.16
    ↓
sshd
    ↓
socadmin
    ↓
Authentication Failures
    ↓
Elastic Agent
    ↓
Elastic SIEM
    ↓
4 Matching Events
    ↓
Threshold >= 3
    ↓
Medium Alert
    ↓
SOC Investigation
```

---

# 20. Investigation Classification

Based on the evidence collected:

```text
Activity:
SSH password-guessing attempt

Source:
192.168.1.10

Target:
soc-linux / 192.168.1.16

Account:
socadmin

Service:
SSH / TCP 22

Authentication result:
Failed

Successful compromise:
Not demonstrated
```

The evidence supports classification as a **detected SSH password-guessing activity** within the controlled laboratory environment.

---

# 21. MITRE Investigation Context

The strongest ATT&CK mapping is:

```text
T1110.001
Brute Force: Password Guessing
```

Supporting service context:

```text
T1021.004
Remote Services: SSH
```

The investigation does not support mapping:

```text
T1078
Valid Accounts
```

because successful use of valid credentials was not demonstrated.

---

# 22. Investigation Evidence

### Alert Overview

```text
screenshots/investigation/01-alert-overview.png
```

### Alert Metadata

```text
screenshots/investigation/02-alert-table-metadata.png
```

### Source/Event Correlation

```text
screenshots/investigation/04-source-events-correlation.png
```

### Endpoint SSH Evidence

```text
screenshots/investigation/05-linux-ssh-journal-current-attack.png
```

### Attack Evidence

```text
screenshots/attack/02-kali-hydra-detection-test.png
```

---

# 23. Investigation Findings

### Finding 1 — Suspicious Source

```text
192.168.1.10
```

was responsible for the observed authentication failures.

### Finding 2 — Target

The activity targeted:

```text
soc-linux
192.168.1.16
```

### Finding 3 — Account

The targeted account was:

```text
socadmin
```

### Finding 4 — Service

The activity targeted:

```text
SSH
TCP/22
```

### Finding 5 — Repeated Failures

Elastic recorded:

```text
4 authentication_failure events
```

within the isolated detection-test window.

### Finding 6 — Detection

The threshold rule generated:

```text
Medium severity
Risk score 47
```

### Finding 7 — Authentication Outcome

The controlled attack produced:

```text
0 valid password found
```

### Finding 8 — Compromise

Successful compromise was:

```text
Not demonstrated
```

---

# 24. Investigation Conclusion

The evidence establishes the following sequence:

```text
Controlled Kali SSH Password Guessing
              ↓
Multiple Authentication Failures
              ↓
Target: soc-linux / socadmin
              ↓
SSH / sshd Telemetry
              ↓
Elastic Agent
              ↓
Elastic SIEM
              ↓
4 Matching Events
              ↓
Threshold Detection
              ↓
Medium Alert
              ↓
SOC Investigation
```

The investigation confirms that the detection correctly identified the controlled SSH password-guessing activity.

The evidence does **not** demonstrate successful authentication or host compromise.

---

# 25. Investigation Status

```text
[✓] Alert validated
[✓] Source identified
[✓] Target identified
[✓] User identified
[✓] Process identified
[✓] SSH service identified
[✓] Elastic events correlated
[✓] Endpoint logs correlated
[✓] Attack evidence correlated
[✓] Timeline established
[✓] Timezone difference validated
[✓] Authentication outcome checked
[✓] Compromise claim avoided
[✓] MITRE context established
[✓] Evidence captured
```


