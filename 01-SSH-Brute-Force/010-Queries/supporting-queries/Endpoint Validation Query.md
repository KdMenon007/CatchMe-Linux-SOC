# Endpoint Validation Query

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

---

# 1. Query Objective

The purpose of this supporting query is to validate the SSH authentication activity directly on the Linux endpoint and compare it with the telemetry observed in Elastic SIEM.

Endpoint validation provides an additional evidence layer between:

```text
Kali Attacker
      ↓
SSH Attack
      ↓
Linux Endpoint
      ↓
Elastic Agent
      ↓
Elastic SIEM
```
The objective is to confirm that the authentication failures observed in Elastic originated from the Linux SSH service on `soc-linux`.

---

# 2. Endpoint Under Investigation

| Attribute          | Value                  |
| ------------------ | ---------------------- |
| Hostname           | `soc-linux`            |
| IP Address         | `192.168.1.16`         |
| Operating System   | Ubuntu 24.04.4 LTS     |
| SSH Service        | `sshd`                 |
| Target Account     | `socadmin`             |
| Attacker IP        | `192.168.1.10`         |
| SIEM               | Elastic                |
| Endpoint Telemetry | Elastic Agent + Auditd |

---

# 3. Linux SSH Journal Query

The Linux SSH service journal was queried using:

```bash
sudo journalctl -u ssh --since "2026-10-01 03:27:00" --until "2026-10-01 03:30:00" --no-pager
```

The query covers the UTC timeframe corresponding to the observed attack window.

---

# 4. Endpoint Evidence

The Linux SSH journal showed authentication activity originating from:

```text
192.168.1.10
```

The targeted account was:

```text
socadmin
```

The SSH service generated multiple authentication-related messages during the attack window.

Observed activity included:

```text
03:27:17 UTC
SSH connection / pre-authentication activity

03:27:17 UTC
Authentication failure from 192.168.1.10
User: socadmin

03:27:19 UTC
Failed password

03:27:22 UTC
Failed password

03:27:23 UTC
Connection closed / PAM authentication activity

03:27:25 UTC
Failed password

03:27:26 UTC
Connection closed / PAM authentication activity
```

---

# 5. Endpoint Validation Flow

```text
┌──────────────────────┐
│ Kali Attacker        │
│ 192.168.1.10         │
└──────────┬───────────┘
           │
           │ SSH authentication attempts
           ▼
┌──────────────────────┐
│ Linux Endpoint       │
│ 192.168.1.16         │
│ soc-linux             │
└──────────┬───────────┘
           │
           │ sshd / PAM
           ▼
┌──────────────────────┐
│ Linux Journal        │
│ Authentication       │
│ Failures             │
└──────────┬───────────┘
           │
           │ Elastic Agent
           ▼
┌──────────────────────┐
│ Elastic SIEM         │
│ authentication_      │
│ failure events       │
└──────────────────────┘
```

---

# 6. Source IP Validation

The endpoint journal identified:

```text
Source IP:
192.168.1.10
```

This matches the fixed Kali attacker address used for Project 01.

The Elastic authentication-failure events also identified:

```text
source.ip:
192.168.1.10
```

This provides endpoint-to-SIEM source correlation.

---

# 7. Account Validation

The targeted SSH account was:

```text
socadmin
```

The Linux journal showed authentication failures involving:

```text
user:
socadmin
```

The same target account was used during the controlled Hydra validation.

This establishes consistency between:

```text
Attack Configuration
        ↓
Linux SSH Journal
        ↓
Elastic Authentication Events
```

---

# 8. SSH Service Validation

The endpoint evidence originated from the SSH service.

Relevant process/service context:

```text
Service:
ssh

Process:
sshd
```

Elastic telemetry also identified:

```text
process.name:
sshd
```

This provides process-level correlation between the endpoint journal and SIEM events.

---

# 9. Time Correlation

The Linux endpoint reports journal timestamps in UTC.

The beginning of the observed endpoint activity was:

```text
03:27:17 UTC
```

The equivalent IST time is:

```text
08:57:17 IST
```

This corresponds with the beginning of the Hydra validation:

```text
08:57:17 IST
```

The correlation is therefore:

```text
Kali
08:57:17 IST
     │
     ▼
Linux
03:27:17 UTC
     │
     ▼
Elastic
08:57:17.887 IST
08:57:17.888 IST
```

---

# 10. Elastic Event Validation

The corresponding Elastic events were observed during:

```text
08:55 IST → 09:00 IST
```

Observed authentication-failure events included:

```text
08:57:17.887
08:57:17.888
08:57:23.642
08:57:26.518
```

Common attributes included:

```text
host.name:
soc-linux

source.ip:
192.168.1.10

user:
socadmin

process.name:
sshd

event.action:
authentication_failure
```

---

# 11. Endpoint-to-SIEM Correlation

```text
LINUX JOURNAL
03:27:17 UTC
      │
      │ Timezone conversion
      ▼
08:57:17 IST
      │
      ▼
ELASTIC EVENT
08:57:17.887
      │
      ├── source.ip = 192.168.1.10
      ├── host.name = soc-linux
      ├── process.name = sshd
      └── event.action = authentication_failure
```

Additional endpoint events were also consistent with the Elastic event sequence.

---

# 12. Authentication Failure Validation

The endpoint evidence demonstrates repeated authentication failures rather than a successful SSH login.

The observed Linux journal activity included multiple:

```text
Failed password
```

and:

```text
authentication failure
```

messages.

The Hydra result independently showed:

```text
5 tries
0 valid password found
```

Therefore, the endpoint evidence supports failed authentication activity without establishing successful credential compromise.

---

# 13. Endpoint State Validation

The investigation did not identify evidence in the collected endpoint telemetry indicating that the controlled brute-force validation resulted in a successful SSH session.

The observed activity remained within the authentication-failure stage:

```text
SSH Connection
      ↓
Authentication Attempt
      ↓
Authentication Failure
      ↓
Connection Closed
```

No successful login should be inferred from the authentication-failure evidence.

---

# 14. Evidence Correlation Matrix

| Endpoint Evidence         | Elastic Evidence                       | Result     |
| ------------------------- | -------------------------------------- | ---------- |
| Source `192.168.1.10`     | `source.ip: 192.168.1.10`              | Correlated |
| User `socadmin`           | User `socadmin`                        | Correlated |
| SSH service               | `process.name: sshd`                   | Correlated |
| Authentication failure    | `event.action: authentication_failure` | Correlated |
| `03:27:17 UTC`            | `08:57:17 IST` event window            | Correlated |
| Multiple failed passwords | Multiple authentication-failure events | Correlated |

---

# 15. Investigation Logic

The endpoint validation follows this process:

```text
1. Identify the affected Linux endpoint
              ↓
2. Query SSH service journal
              ↓
3. Identify source IP
              ↓
4. Identify targeted account
              ↓
5. Identify authentication result
              ↓
6. Normalize timestamps
              ↓
7. Compare with Elastic events
              ↓
8. Compare with attack execution
              ↓
9. Determine whether compromise occurred
```

---

# 16. Evidence Chain

The collected evidence forms the following chain:

```text
Kali Hydra
192.168.1.10
      │
      ▼
SSH Authentication Attempts
      │
      ▼
Linux sshd
      │
      ▼
PAM Authentication Failures
      │
      ▼
Linux Journal
      │
      ▼
Elastic Agent
      │
      ▼
Elastic SIEM
      │
      ▼
Detection Rule
      │
      ▼
SOC Alert
```

---

# 17. Endpoint Validation Result

The endpoint validation confirms that the Linux SSH service recorded authentication failures originating from the controlled Kali attacker.

The following were successfully correlated:

```text
Attacker:
192.168.1.10

Target:
192.168.1.16

Target account:
socadmin

Service:
SSH

Process:
sshd

Activity:
Authentication failures
```

---

# 18. Successful Compromise Assessment

Endpoint validation does not show successful SSH authentication during this test.

The attack result was:

```text
Brute-force activity:
CONFIRMED

Endpoint authentication failures:
CONFIRMED

Elastic telemetry:
CONFIRMED

Detection:
CONFIRMED

Successful SSH authentication:
NOT OBSERVED
```

The Project 01 validation therefore remains a controlled brute-force detection exercise rather than a demonstrated successful compromise.

---

# 19. Evidence Path

Supporting endpoint evidence is stored under:

```text
evidence/
screenshots/
queries/
```

Relevant screenshots include:

```text
screenshots/telemetry/
01-linux-ssh-journal-attack-evidence.png

screenshots/investigation/
05-linux-ssh-journal-current-attack.png
06-linux-ssh-journal-previous-attack.png
```

---

# 20. Final Finding

The Linux endpoint directly recorded the SSH authentication failures generated during the controlled attack.

The endpoint evidence correlated with Elastic telemetry through:

```text
Source IP
Target host
Target account
SSH process
Authentication action
Timestamp
```

This establishes an endpoint-level evidence chain supporting the SOC investigation.

---

# 21. Status

**Endpoint Validation: COMPLETED**

The Linux SSH journal was validated against the Elastic authentication-failure telemetry and the controlled Kali attack timeline.

**Final assessment:** Authentication-failure activity was confirmed; successful SSH compromise was not observed.

