# Project 01 — Investigation Timeline

## 1. Purpose

This document records the chronological sequence of events observed during the Project 01 SSH brute-force detection and investigation.

The timeline correlates:

- Kali attack execution
- Linux SSH telemetry
- Elastic SIEM events
- Detection processing
- Alert generation
- SOC investigation

The timeline uses **IST** for the primary investigation view. Linux endpoint timestamps are converted from UTC to IST where required.

---

# 2. Incident Details

| Attribute | Value |
|---|---|
| Project | CatchMe Linux SOC — Project 01 |
| Scenario | SSH Brute Force |
| Incident ID | `CATCHME-LNX-001` |
| Attacker | Kali |
| Attacker IP | `192.168.1.10` |
| Target | `soc-linux` |
| Target IP | `192.168.1.16` |
| Target account | `socadmin` |
| Service | SSH |
| Port | `22/TCP` |
| Detection rule | `CatchMe - Linux SSH Brute Force Detection` |

---

# 3. Timezone Reference

## Kali

```text
Timezone:
Asia/Kolkata

Offset:
UTC+05:30
```

## soc-linux

```text
Timezone:
Etc/UTC

Offset:
UTC+00:00
```

## Conversion

```text
UTC + 05:30 = IST
```

Examples:

```text
03:27:17 UTC = 08:57:17 IST
03:27:26 UTC = 08:57:26 IST
03:28:02 UTC = 08:58:02 IST
```

---

# 4. Attack Timeline

| Time (IST)   | Event                                                         | Source       | Evidence                                                            |
| ------------ | ------------------------------------------------------------- | ------------ | ------------------------------------------------------------------- |
| 08:57:17     | Hydra SSH attack begins                                       | Kali         | `screenshots/attack/02-kali-hydra-detection-test.png`               |
| 08:57:17     | Initial SSH authentication failures recorded                  | `soc-linux`  | `screenshots/investigation/05-linux-ssh-journal-current-attack.png` |
| 08:57:19     | Failed SSH password attempts continue                         | `soc-linux`  | `screenshots/investigation/05-linux-ssh-journal-current-attack.png` |
| 08:57:22     | Additional failed password attempts recorded                  | `soc-linux`  | `screenshots/investigation/05-linux-ssh-journal-current-attack.png` |
| 08:57:23     | Additional authentication failure observed in Elastic         | Elastic SIEM | `screenshots/investigation/04-source-events-correlation.png`        |
| 08:57:25     | Final failed password activity recorded                       | `soc-linux`  | `screenshots/investigation/05-linux-ssh-journal-current-attack.png` |
| 08:57:26     | SSH connection closes during pre-authentication               | `soc-linux`  | `screenshots/investigation/05-linux-ssh-journal-current-attack.png` |
| 08:57:26     | Hydra finishes with `0 valid password found`                  | Kali         | `screenshots/attack/02-kali-hydra-detection-test.png`               |
| 08:57:26.518 | Elastic records original event time associated with detection | Elastic SIEM | `screenshots/investigation/02-alert-table-metadata.png`             |
| 08:58:02.247 | Threshold detection alert generated                           | Elastic SIEM | `screenshots/detection/05-brute-force-alert-generated.png`          |
| 08:58:02.247 | Alert opens with Medium severity and risk score 47            | Elastic SIEM | `screenshots/investigation/01-alert-overview.png`                   |

---

# 5. Endpoint Timeline — UTC

The Linux SSH journal recorded the following activity:

```text
03:27:17 UTC
Received disconnect / pre-authentication activity

03:27:17 UTC
Authentication failure for socadmin

03:27:17 UTC
Authentication failure for socadmin

03:27:19 UTC
Failed password for socadmin

03:27:19 UTC
Failed password for socadmin

03:27:22 UTC
Failed password for socadmin

03:27:22 UTC
Failed password for socadmin

03:27:23 UTC
Connection closed during authentication

03:27:25 UTC
Failed password for socadmin

03:27:26 UTC
Connection closed during authentication
```

---

# 6. Endpoint Timeline — IST

After timezone normalization:

```text
08:57:17 IST
Authentication failures begin

08:57:19 IST
Failed passwords

08:57:22 IST
Additional failed passwords

08:57:23 IST
Authentication activity observed in Elastic

08:57:25 IST
Final failed password activity

08:57:26 IST
SSH connection closes

08:57:26 IST
Hydra reports 0 valid passwords
```

---

# 7. Elastic Event Timeline

The isolated Elastic investigation window was:

```text
08:55–09:00 IST
```

The query was:

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```

The investigation returned four matching events:

| Elastic Time (IST) | Source IP      | User       | Event Action             | Process | Host        |
| ------------------ | -------------- | ---------- | ------------------------ | ------- | ----------- |
| 08:57:17.887       | `192.168.1.10` | `socadmin` | `authentication_failure` | `sshd`  | `soc-linux` |
| 08:57:17.888       | `192.168.1.10` | `socadmin` | `authentication_failure` | `sshd`  | `soc-linux` |
| 08:57:23.642       | `192.168.1.10` | `socadmin` | `authentication_failure` | `sshd`  | `soc-linux` |
| 08:57:26.518       | `192.168.1.10` | `socadmin` | `authentication_failure` | `sshd`  | `soc-linux` |

---

# 8. Detection Timeline

The detection rule was configured with:

```text
Rule:
CatchMe - Linux SSH Brute Force Detection

Type:
Threshold

Group by:
source.ip

Threshold:
>= 3
```

During the attack:

```text
Matching events = 4
Threshold = 3
```

Therefore:

```text
4 >= 3
```

The threshold condition was satisfied.

---

# 9. Alert Timeline

### Original Event Time

```text
08:57:26.518 IST
```

### Alert Generation

```text
08:58:02.247 IST
```

### Last Detected

```text
08:58:02.254 IST
```

### Alert Properties

```text
Severity:
Medium

Risk score:
47

Status:
Open
```

---

# 10. End-to-End Timeline

```text
08:57:17
     │
     ▼
┌─────────────────────────────┐
│ Kali launches Hydra SSH     │
│ Source: 192.168.1.10        │
└──────────────┬──────────────┘
               │
               ▼
08:57:17
┌─────────────────────────────┐
│ SSH authentication failures │
│ Target: soc-linux            │
│ User: socadmin              │
└──────────────┬──────────────┘
               │
               ▼
08:57:19–08:57:25
┌─────────────────────────────┐
│ Failed password attempts    │
└──────────────┬──────────────┘
               │
               ▼
08:57:23+
┌─────────────────────────────┐
│ Elastic authentication      │
│ failure telemetry           │
└──────────────┬──────────────┘
               │
               ▼
08:57:26
┌─────────────────────────────┐
│ Hydra completes             │
│ 0 valid passwords           │
└──────────────┬──────────────┘
               │
               ▼
08:57:26.518
┌─────────────────────────────┐
│ Elastic original event      │
└──────────────┬──────────────┘
               │
               ▼
08:58:02.247
┌─────────────────────────────┐
│ Threshold alert generated   │
│ Medium / Risk 47            │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ SOC Investigation           │
└─────────────────────────────┘
```

---

# 11. Cross-Source Correlation

The timeline establishes the following relationship:

```text
Kali
192.168.1.10
     │
     │ 08:57:17 IST
     │
     ▼
SSH TCP/22
     │
     ▼
soc-linux
192.168.1.16
     │
     ▼
sshd
     │
     ▼
socadmin
     │
     ▼
Authentication Failures
     │
     ▼
Elastic Agent
     │
     ▼
Elastic SIEM
     │
     ▼
4 Matching Events
     │
     ▼
Threshold >= 3
     │
     ▼
08:58:02 Alert
```

---

# 12. Attack Window

The primary attack window was:

```text
08:57:17–08:57:26 IST
```

Duration:

```text
Approximately 9 seconds
```

During this period:

* Hydra executed the SSH password-guessing test.
* Linux recorded repeated authentication failures.
* Elastic received authentication-failure telemetry.
* The attack ended with no valid password found.

---

# 13. Detection Window

The detection alert was generated at:

```text
08:58:02.247 IST
```

The alert was therefore generated after the attack activity had completed.

The observed relationship was:

```text
Attack activity
      ↓
Telemetry ingestion
      ↓
Detection evaluation
      ↓
Alert generation
```

---

# 14. Investigation Window

The investigation was intentionally restricted to:

```text
08:55–09:00 IST
```

This prevented the earlier Project 01 attack test around 08:29 IST from being mixed with the detection-validation run.

The isolated window produced exactly four matching authentication-failure events.

---

# 15. Earlier Test Exclusion

An earlier Hydra test occurred around:

```text
08:29 IST
```

Those events were intentionally excluded from the final investigation window.

The final evidence set therefore represents:

```text
Current detection-validation attack
+
Current endpoint telemetry
+
Current Elastic events
+
Current alert
```

rather than combining both attack executions.

---

# 16. Authentication Outcome

The final attack output was:

```text
0 valid password found
```

The endpoint recorded:

```text
Failed password
```

and:

```text
authentication failure
```

The observed SSH connections ended during pre-authentication.

Therefore:

```text
Successful authentication:
Not observed

Successful compromise:
Not demonstrated
```

---

# 17. Timeline Evidence

## Attack

```text
screenshots/attack/02-kali-hydra-detection-test.png
```

## Hunting

```text
screenshots/hunting/01-kql-authentication-failures.png
screenshots/hunting/02-kql-sshd-source-correlation.png
```

## Detection

```text
screenshots/detection/05-brute-force-alert-generated.png
```

## Investigation

```text
screenshots/investigation/01-alert-overview.png
screenshots/investigation/02-alert-table-metadata.png
screenshots/investigation/04-source-events-correlation.png
screenshots/investigation/05-linux-ssh-journal-current-attack.png
```

---

# 18. Timeline Integrity Checks

```text
[✓] Attacker IP matches
[✓] Target IP matches
[✓] Target hostname matches
[✓] Target account matches
[✓] SSH service matches
[✓] Endpoint timestamps validated
[✓] Kali timestamps validated
[✓] UTC converted to IST
[✓] Elastic timestamps correlated
[✓] Attack result correlated
[✓] Detection timestamp recorded
[✓] Earlier test excluded
```

---

# 19. Final Timeline

```text
08:57:17 IST
Hydra starts
        ↓
08:57:17 IST
SSH authentication failures begin
        ↓
08:57:19 IST
Failed passwords
        ↓
08:57:22 IST
Additional failed passwords
        ↓
08:57:23 IST
Elastic authentication-failure event
        ↓
08:57:25 IST
Final failed password activity
        ↓
08:57:26 IST
SSH connection closes
        ↓
08:57:26 IST
Hydra: 0 valid passwords
        ↓
08:57:26.518 IST
Elastic original event
        ↓
08:58:02.247 IST
Threshold alert generated
        ↓
SOC Investigation
        ↓
No successful compromise demonstrated
```

---

# 20. Timeline Conclusion

The Project 01 timeline establishes a consistent sequence from controlled attack execution through endpoint telemetry, Elastic ingestion, automated detection, and SOC investigation.

The strongest confirmed sequence is:

```text
Kali SSH Password Guessing
        ↓
Linux SSH Authentication Failures
        ↓
Elastic Telemetry
        ↓
4 Matching Events
        ↓
Threshold >= 3
        ↓
Medium Alert
        ↓
SOC Investigation
        ↓
No Successful Authentication Demonstrated
```

The timeline provides the chronological evidence required for the Project 01 incident report and MITRE ATT&CK mapping.

---

## Timeline Status

```text
[✓] Attack start established
[✓] Authentication-failure period established
[✓] Endpoint events correlated
[✓] Elastic events correlated
[✓] Alert time established
[✓] Timezones normalized
[✓] Earlier test excluded
[✓] Authentication outcome established
[✓] Timeline evidence captured
```

