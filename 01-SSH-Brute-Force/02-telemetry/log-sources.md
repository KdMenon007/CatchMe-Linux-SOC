# Project 01 — Log Sources

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Scenario:** SSH Brute Force → Compromise  
**Endpoint:** `soc-linux` (`192.168.1.16`)  
**SIEM:** `elastic-siem` (`192.168.1.11`)  
**Attacker:** Kali (`192.168.1.10`)

---

## 1. Purpose

This document identifies the telemetry and log sources used during the Project 01 investigation.

The purpose is to establish:

- Where the SSH activity originated
- Where authentication failures were generated
- How endpoint telemetry was collected
- How telemetry reached Elastic SIEM
- Which structured fields were used for hunting and detection
- Which source provided the primary evidence for the incident

---

# 2. Project 01 Log-Source Architecture

```text
┌──────────────────────────────┐
│       Kali Attacker          │
│       192.168.1.10           │
│                              │
│       Hydra / SSH            │
└──────────────┬───────────────┘
               │
               │ SSH Authentication
               ▼
┌──────────────────────────────┐
│        soc-linux             │
│       192.168.1.16           │
│                              │
│            sshd              │
└──────────────┬───────────────┘
               │
               ├──────────────► Linux Journal
               │
               ├──────────────► Authentication Events
               │
               └──────────────► Auditd
                                │
                                ▼
                       ┌──────────────────┐
                       │  Elastic Agent   │
                       │      9.5.2       │
                       └────────┬─────────┘
                                │
                                ▼
                       ┌──────────────────┐
                       │  Fleet Server   │
                       │ 192.168.1.11     │
                       └────────┬─────────┘
                                │
                                ▼
                       ┌──────────────────┐
                       │   Elastic SIEM  │
                       │     Kibana      │
                       └──────────────────┘
```
---

# 3. Primary Log Source — SSH / sshd

The primary source for Project 01 is the Linux SSH service.

```text
Service:
sshd

Protocol:
SSH

Port:
TCP/22

Host:
soc-linux

IP:
192.168.1.16
```

The SSH daemon generated authentication-related events when the controlled password-guessing activity occurred.

---

# 4. SSH Authentication Events

The endpoint recorded authentication failures including:

```text
pam_unix(sshd:auth): authentication failure
```

and:

```text
Failed password for socadmin from 192.168.1.10
```

These events provide direct endpoint evidence of the password-guessing activity.

Important investigation attributes include:

```text
Source IP
Target username
Authentication result
SSH process
Target host
Timestamp
```

---

# 5. Linux Journal

The Linux journal provided the endpoint-level historical evidence used during Project 01.

The investigation examined the SSH service journal using:

```text
journalctl -u ssh
```

The relevant attack window contained events corresponding to:

```text
03:27:17 UTC
03:27:19 UTC
03:27:22 UTC
03:27:23 UTC
03:27:25 UTC
03:27:26 UTC
```

These timestamps correspond to the Kali attack window after UTC/IST normalization.

---

# 6. Authentication Failure Evidence

The SSH journal recorded:

```text
pam_unix(sshd:auth): authentication failure
```

followed by repeated:

```text
Failed password
```

events.

The common source was:

```text
192.168.1.10
```

The targeted account was:

```text
socadmin
```

The SSH process was:

```text
sshd
```

This source provides the strongest endpoint-side evidence for Project 01.

---

# 7. Elastic Agent

Elastic Agent operates on:

```text
soc-linux
192.168.1.16
```

Agent version:

```text
9.5.2
```

Expected state during Project 01:

```text
fleet
└─ status: (HEALTHY) Connected

elastic-agent
└─ status: (HEALTHY) Running
```

The agent provides the collection and forwarding layer between the Linux endpoint and the Elastic environment.

---

# 8. Fleet Server

Fleet Server operates on:

```text
elastic-siem
192.168.1.11
```

Endpoint communication:

```text
soc-linux
192.168.1.16
       │
       │ TCP/8220
       ▼
elastic-siem
192.168.1.11
```

Fleet Server health was verified through:

```text
https://elastic-siem:8220/api/status
```

The API returned:

```json
{
  "name": "fleet-server",
  "status": "HEALTHY"
}
```

---

# 9. Elastic SIEM

Elastic SIEM receives the telemetry used for Project 01 hunting and detection.

The SIEM environment is:

```text
Hostname:
elastic-siem

IP:
192.168.1.11
```

Kibana provides the investigation interface used to inspect the normalized events.

---

# 10. Structured ECS Fields

The Project 01 investigation identified the following fields in Elastic:

| Field          | Purpose                           | Observed Value           |
| -------------- | --------------------------------- | ------------------------ |
| `source.ip`    | Origin of authentication activity | `192.168.1.10`           |
| `user.name`    | Target account                    | `socadmin`               |
| `event.action` | Authentication result             | `authentication_failure` |
| `process.name` | Process responsible               | `sshd`                   |
| `host.name`    | Endpoint                          | `soc-linux`              |

These fields are more useful for detection than relying only on raw log text.

The project methodology also emphasizes inspecting actual events before assuming field names. 

---

# 11. Auditd

Auditd was enabled on the Linux endpoint before the Project 01 attack.

Baseline:

```text
enabled = 1
lost    = 0
backlog = 0
```

Auditd is an additional Linux security telemetry source.

For Project 01, however, the primary evidence for SSH password guessing came from SSH/system authentication telemetry rather than a specific Auditd rule.

Auditd becomes particularly useful for later Linux investigations involving process execution, privilege activity, file activity, persistence, and suspicious shell behavior.

---

# 12. Source-to-Field Relationship

```text
SSH / sshd
    │
    ├── Source IP
    │      └── source.ip
    │
    ├── Target account
    │      └── user.name
    │
    ├── Authentication result
    │      └── event.action
    │
    ├── SSH process
    │      └── process.name
    │
    └── Target endpoint
           └── host.name
```

This field relationship is the foundation of the Project 01 detection.

---

# 13. Threat-Hunting Source

The primary KQL investigation query was:

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```

The isolated detection-validation window returned:

```text
4 matching events
```

The four events shared:

```text
source.ip    = 192.168.1.10
user.name    = socadmin
event.action = authentication_failure
process.name = sshd
host.name    = soc-linux
```

---

# 14. Detection Source

The detection rule uses:

```kql
event.action : "authentication_failure" and process.name : "sshd" and host.name : "soc-linux"
```

The detection aggregates matching events by:

```text
source.ip
```

Threshold:

```text
>= 3
```

The controlled detection-validation attack generated:

```text
4 matching events
```

This caused the Project 01 detection alert to fire.

---

# 15. Evidence Hierarchy

Project 01 uses multiple sources to avoid relying on a single event.

```text
Level 1
Attack Tool
    │
    ▼
Hydra Result
    │
    ▼
Level 2
Endpoint SSH Journal
    │
    ▼
Level 3
Elastic SIEM Events
    │
    ▼
Level 4
Detection Alert
    │
    ▼
Level 5
Correlated Investigation Timeline
```

This provides independent correlation between attacker activity, endpoint telemetry, SIEM events, and detection.

---

# 16. Source Correlation

| Source           | Evidence                                    |
| ---------------- | ------------------------------------------- |
| Kali             | Hydra executed against `192.168.1.16:22`    |
| SSH              | Authentication failures                     |
| Linux journal    | Failed password / PAM authentication events |
| Elastic Agent    | Endpoint telemetry collection               |
| Fleet            | Agent management and connectivity           |
| Elastic SIEM     | Normalized security events                  |
| Kibana           | Event investigation                         |
| Detection Engine | Threshold alert                             |
| Investigation    | Correlated timeline                         |

---

# 17. Time Correlation

Kali uses:

```text
Asia/Kolkata
IST
UTC+05:30
```

Linux uses:

```text
Etc/UTC
UTC+00:00
```

Therefore:

```text
03:27:17 UTC
=
08:57:17 IST
```

and:

```text
03:27:26 UTC
=
08:57:26 IST
```

Timezone normalization is required when correlating the attacker terminal, Linux journal, Elastic events, and detection alert.

---

# 18. Primary vs Supporting Sources

### Primary

```text
sshd / Linux authentication telemetry
```

This directly records the failed SSH authentication attempts.

### Supporting

```text
Kali Hydra output
Elastic Agent
Fleet
Elastic SIEM
Kibana detection alert
Auditd
```

These sources provide execution context, transport, centralized visibility, and correlation.

---

# 19. Project 01 Log-Source Matrix

| Source        | Host           | Role              | Project 01 Usage                       |
| ------------- | -------------- | ----------------- | -------------------------------------- |
| Hydra         | Kali           | Attack execution  | Confirmed controlled password guessing |
| `sshd`        | `soc-linux`    | SSH service       | Primary authentication evidence        |
| Linux journal | `soc-linux`    | Endpoint logging  | Authentication failure timeline        |
| Auditd        | `soc-linux`    | Security auditing | Supporting telemetry                   |
| Elastic Agent | `soc-linux`    | Collection        | Endpoint-to-Fleet telemetry            |
| Fleet Server  | `elastic-siem` | Agent management  | Connectivity validation                |
| Elastic SIEM  | `elastic-siem` | Central analysis  | Hunting and detection                  |
| Kibana        | `elastic-siem` | Investigation     | Event and alert analysis               |

---

# 20. Log-Source Validation Checklist

* [x] SSH identified as primary Project 01 source
* [x] `sshd` authentication failures confirmed
* [x] Linux journal confirmed
* [x] Source IP identified
* [x] Target account identified
* [x] Target host identified
* [x] Elastic Agent confirmed healthy
* [x] Fleet Server confirmed healthy
* [x] Elastic SIEM confirmed as analysis layer
* [x] ECS fields identified from actual events
* [x] Auditd identified as supporting Linux telemetry
* [x] Timezone differences documented
* [x] Source correlation documented

---

# 21. Log-Source Conclusion

Project 01 has a complete telemetry chain:

```text
Kali
192.168.1.10
      │
      │ SSH password guessing
      ▼
sshd
      │
      │ Authentication failure
      ▼
Linux Journal
      │
      ▼
Elastic Agent
      │
      ▼
Fleet
      │
      ▼
Elastic SIEM
      │
      ├── KQL Hunting
      │
      └── Detection Engine
              │
              ▼
             Alert
```

The primary evidence source is Linux SSH authentication telemetry.

Elastic SIEM provides the centralized hunting, detection, alerting, and investigation layer.

---

## 22. Evidence

```text
screenshots/
├── telemetry/
│   └── 01-elastic-ssh-authentication-events.png
└── attack/
    └── 02-kali-hydra-detection-test.png
```


