# Project 01 — Investigation Findings

## 1. Purpose

This document records the confirmed findings from the Project 01 SOC investigation.

The findings are based on the correlated evidence collected from:

- Kali attacker telemetry
- Linux SSH journal
- Elastic SIEM
- Detection alert
- KQL investigation
- Time-correlated event data

---

# 2. Incident Identification

| Attribute | Finding |
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
| Process | `sshd` |
| Detection | `CatchMe - Linux SSH Brute Force Detection` |
| Severity | Medium |
| Risk score | 47 |
| Alert status | Open |

---

# 3. Primary Finding

The investigation confirmed a controlled SSH password-guessing activity originating from:

```text
192.168.1.10
```

against:

```text
soc-linux
192.168.1.16
```

targeting:

```text
socadmin
```

through:

```text
SSH / TCP 22
```

The activity generated multiple authentication failures and triggered the Elastic threshold detection.

---

# 4. Finding — Repeated Authentication Failures

Elastic recorded four matching authentication-failure events during the isolated detection-test window.

Observed fields:

```text
source.ip     = 192.168.1.10
user.name     = socadmin
event.action  = authentication_failure
process.name  = sshd
host.name     = soc-linux
```

This evidence is consistent across the correlated Elastic events.

### Finding

```text
Repeated SSH authentication failures:
CONFIRMED
```

---

# 5. Finding — Source Correlation

The suspicious source was:

```text
192.168.1.10
```

This corresponds to the Kali attacker machine used for the controlled Project 01 test.

The same source IP appeared in:

* Hydra attack execution
* Linux SSH journal
* Elastic authentication events
* Elastic detection alert

### Finding

```text
Source correlation:
CONFIRMED
```

---

# 6. Finding — Target Correlation

The target endpoint was:

```text
soc-linux
192.168.1.16
```

The activity was associated with the Linux SSH service.

### Finding

```text
Target correlation:
CONFIRMED
```

---

# 7. Finding — Account Targeting

The targeted account was:

```text
socadmin
```

The Linux SSH journal and Elastic events both associated the authentication failures with this account.

### Finding

```text
Target account:
socadmin

Account targeting:
CONFIRMED
```

---

# 8. Finding — SSH Service

The activity targeted:

```text
SSH
TCP/22
```

The endpoint process associated with the activity was:

```text
sshd
```

### Finding

```text
SSH targeting:
CONFIRMED
```

---

# 9. Finding — Detection Trigger

The Elastic detection used:

```kql
event.action : "authentication_failure" and process.name : "sshd" and host.name : "soc-linux"
```

Events were grouped by:

```text
source.ip
```

Threshold:

```text
>= 3
```

Observed matching events:

```text
4
```

Therefore:

```text
4 >= 3
```

The detection condition was satisfied.

### Finding

```text
Detection trigger:
CONFIRMED
```

---

# 10. Finding — Alert Generation

Elastic generated:

```text
Rule:
CatchMe - Linux SSH Brute Force Detection
```

Alert properties:

```text
Severity:
Medium

Risk score:
47

Status:
Open
```

The alert identified:

```text
source = 192.168.1.10
```

and:

```text
threshold count = 4
```

### Finding

```text
Automated detection:
SUCCESSFUL
```

---

# 11. Finding — Endpoint Validation

The Linux SSH journal independently recorded authentication failures originating from:

```text
192.168.1.10
```

against:

```text
socadmin
```

The journal included failed-password activity and SSH pre-authentication connection closures.

### Finding

```text
Endpoint telemetry validation:
CONFIRMED
```

This provides an independent endpoint source supporting the Elastic SIEM investigation.

---

# 12. Finding — Attack Validation

The controlled Hydra execution against:

```text
192.168.1.16:22
```

reported:

```text
0 valid password found
```

This is consistent with the Linux SSH authentication-failure evidence.

### Finding

```text
Controlled password-guessing activity:
CONFIRMED
```

---

# 13. Finding — Successful Authentication

The investigation did not identify evidence demonstrating a successful password authentication resulting from the controlled attack.

The observed evidence consisted of:

```text
authentication_failure
```

and:

```text
Failed password
```

Hydra reported:

```text
0 valid password found
```

### Finding

```text
Successful authentication:
NOT OBSERVED
```

---

# 14. Finding — Successful Compromise

No evidence from this Project 01 execution demonstrates:

* Successful SSH login
* Post-authentication command execution
* Privilege escalation
* Persistence
* Lateral movement
* Data access
* Host compromise

### Finding

```text
Successful compromise:
NOT DEMONSTRATED
```

The project title remains **SSH Brute Force → Compromise** as the scenario name, but this particular controlled execution demonstrated the brute-force phase and detection rather than a successful compromise.

---

# 15. Finding — Timeline Correlation

The attack and telemetry were successfully correlated after accounting for the different system time zones.

### Kali

```text
Asia/Kolkata
UTC+05:30
```

### Linux

```text
Etc/UTC
UTC+00:00
```

Example:

```text
03:27:17 UTC
=
08:57:17 IST
```

The endpoint SSH activity therefore aligns with the Kali attack execution.

### Finding

```text
Cross-system timeline:
CORRELATED
```

---

# 16. Finding — Earlier Test Exclusion

An earlier Project 01 Hydra test occurred around:

```text
08:29 IST
```

The final investigation was restricted to:

```text
08:55–09:00 IST
```

This isolated the second detection-validation attack.

Therefore the four-event investigation result represents the current validation window rather than combining both Project 01 attack runs.

### Finding

```text
Investigation window isolation:
VALIDATED
```

---

# 17. Evidence Correlation

```text
┌──────────────────────┐
│ Kali                 │
│ 192.168.1.10         │
│ Hydra SSH Test       │
└──────────┬───────────┘
           │
           │ SSH TCP/22
           ▼
┌──────────────────────┐
│ soc-linux            │
│ 192.168.1.16         │
│ sshd                 │
│ socadmin             │
└──────────┬───────────┘
           │
           │ Authentication failures
           ▼
┌──────────────────────┐
│ Elastic Agent        │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ Elastic SIEM         │
│ 4 matching events    │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ Threshold Detection  │
│ >= 3 / source.ip     │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ Medium Alert         │
│ Risk 47              │
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ SOC Investigation    │
└──────────────────────┘
```

---

# 18. Evidence Matrix

| Finding                 | Evidence Source               | Result           |
| ----------------------- | ----------------------------- | ---------------- |
| SSH attack executed     | Kali / Hydra                  | Confirmed        |
| Source IP               | Kali + Linux + Elastic        | `192.168.1.10`   |
| Target host             | Linux + Elastic               | `soc-linux`      |
| Target IP               | Lab configuration + telemetry | `192.168.1.16`   |
| Target account          | Linux + Elastic               | `socadmin`       |
| SSH service             | Linux + Elastic               | `sshd` / TCP 22  |
| Authentication failures | Linux + Elastic               | Confirmed        |
| Four matching events    | Elastic Discover              | Confirmed        |
| Threshold reached       | Elastic alert                 | Confirmed        |
| Alert generated         | Elastic Security              | Confirmed        |
| Successful password     | Hydra                         | Not observed     |
| Successful SSH login    | Investigation                 | Not demonstrated |
| Host compromise         | Investigation                 | Not demonstrated |

---

# 19. MITRE ATT&CK Finding

## T1110.001 — Brute Force: Password Guessing

Evidence supports:

```text
Observed
```

The controlled attack repeatedly attempted passwords against the SSH account.

---

## T1021.004 — Remote Services: SSH

Evidence supports:

```text
Supporting context
```

SSH was the remote service targeted by the password-guessing activity.

The investigation does not establish successful remote access.

---

## T1078 — Valid Accounts

Evidence supports:

```text
Not observed
```

No successful use of valid credentials was demonstrated during the controlled attack.

---

# 20. Cyber Kill Chain Finding

| Phase                 | Finding                                   |
| --------------------- | ----------------------------------------- |
| Reconnaissance        | SSH service identified in lab preparation |
| Weaponization         | Controlled password list prepared         |
| Delivery              | SSH authentication traffic delivered      |
| Exploitation          | Repeated password attempts observed       |
| Installation          | Not observed                              |
| Command & Control     | Not observed                              |
| Actions on Objectives | Not observed                              |

The evidence supports the early attack stages associated with the controlled password-guessing activity.

Later-stage compromise behavior was not demonstrated.

---

# 21. Detection Effectiveness

The detection successfully converted the observed behavior into an actionable alert.

```text
Observed authentication failures
          ↓
KQL hunt
          ↓
Detection query
          ↓
Source IP aggregation
          ↓
Threshold >= 3
          ↓
4 matching events
          ↓
Medium alert
          ↓
SOC investigation
```

### Result

```text
Detection:
SUCCESSFUL
```

This does not mean the attack succeeded; it means the SOC detection successfully identified the simulated attack behavior.

---

# 22. Incident Impact Assessment

Based on the evidence collected:

```text
Authentication attack:
Confirmed

Credential compromise:
Not demonstrated

Successful SSH access:
Not demonstrated

Privilege escalation:
Not observed

Persistence:
Not observed

Lateral movement:
Not observed

Data access:
Not observed

Host compromise:
Not demonstrated
```

The demonstrated impact is therefore limited to the controlled authentication-attack activity and its associated telemetry.

---

# 23. Evidence Integrity

The investigation used a controlled and isolated time window.

Validation controls included:

```text
[✓] Correct attacker IP
[✓] Correct target IP
[✓] Correct target hostname
[✓] Correct target account
[✓] Correct SSH service
[✓] Correct investigation window
[✓] Endpoint log correlation
[✓] Elastic event correlation
[✓] Hydra result correlation
[✓] Timezone normalization
[✓] Earlier test excluded
```

No successful credential or compromise was inferred without supporting evidence.

---

# 24. Investigation Finding Summary

```text
┌─────────────────────────────────────────────┐
│ PROJECT 01 FINAL FINDINGS                   │
├─────────────────────────────────────────────┤
│ SSH password guessing       CONFIRMED       │
│ Authentication failures     CONFIRMED       │
│ Source correlation          CONFIRMED       │
│ Target correlation          CONFIRMED       │
│ Account targeting           CONFIRMED       │
│ SSH service targeting       CONFIRMED       │
│ SIEM telemetry              CONFIRMED       │
│ Detection alert             CONFIRMED       │
│ Successful authentication   NOT OBSERVED    │
│ Successful compromise       NOT DEMONSTRATED│
└─────────────────────────────────────────────┘
```

---

# 25. Evidence References

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
screenshots/detection/01-threshold-rule-configuration.png
screenshots/detection/03-rule-created-enabled.png
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

# 26. Final Finding

The Project 01 investigation confirmed that the CatchMe Linux SOC successfully detected and investigated a controlled SSH password-guessing attack originating from Kali `192.168.1.10` against `soc-linux` `192.168.1.16`.

The attack generated four matching SSH authentication-failure events, satisfied the configured threshold of three events from the same source IP, and generated a Medium-severity Elastic alert with risk score 47.

The collected evidence does not demonstrate successful password authentication or host compromise.

Therefore the final investigation conclusion is:

```text
SSH Password Guessing
        ↓
Observed
        ↓
Detected
        ↓
Investigated
        ↓
Successful Authentication
        ↓
Not Observed
        ↓
Successful Compromise
        ↓
Not Demonstrated
```

---

## Investigation Status

```text
[✓] Alert validated
[✓] Source identified
[✓] Target identified
[✓] User identified
[✓] Process identified
[✓] Timeline correlated
[✓] Endpoint evidence validated
[✓] SIEM evidence validated
[✓] Attack result validated
[✓] MITRE context documented
[✓] Impact assessed
[✓] Evidence integrity checked
[✓] Findings documented
```
