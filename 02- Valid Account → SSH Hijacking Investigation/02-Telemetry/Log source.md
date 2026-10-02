# Project 02 — Log Sources

## Project

Project: CatchMe Linux SOC — Project 02  
Scenario: Valid Account → SSH Hijacking  
Endpoint: `soc-linux` (`192.168.1.16`)  
SIEM: `elastic-siem` (`192.168.1.11`)  
Attacker: Kali (`192.168.1.10`)  
Target Account: `socadmin`

---

## 1. Purpose

This document identifies the telemetry and log sources used during the Project 02 investigation.

The purpose is to establish:

- Where the SSH activity originated
- Where authentication events were generated
- How endpoint telemetry was collected
- How telemetry reached Elastic SIEM
- Which structured fields were used for hunting and investigation
- Which sources provided primary evidence
- How authentication and session activity were correlated
- How timestamps were normalized during investigation

---

## 2. Project 02 Log-Source Architecture

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
               ├──────────────► /var/log/auth.log
               │
               └──────────────► SSH Authentication Events
                                │
                                ▼
                       ┌──────────────────┐
                       │  Elastic Agent   │
                       │      9.5.2       │
                       └────────┬─────────┘
                                │
                                ▼
                       ┌──────────────────┐
                       │  Fleet Server    │
                       │  192.168.1.11    │
                       └────────┬─────────┘
                                │
                                ▼
                       ┌──────────────────┐
                       │   Elastic SIEM   │
                       │      Kibana      │
                       └──────────────────┘
```

---

## 3. Primary Log Source — SSH / sshd

The primary telemetry source for Project 02 is the Linux SSH service.

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

The SSH daemon generated authentication and session-related events during the controlled valid-account SSH activity.

Project 02 therefore relies heavily on SSH authentication telemetry for establishing the relationship between:

```text
Source IP
    +
Target Account
    +
Authentication Result
    +
SSH Service
    +
Session Activity
```

---

## 4. SSH Authentication Events

The endpoint recorded authentication activity involving:

```text
Source:
192.168.1.10

Target Account:
socadmin

Service:
sshd
```

Observed authentication-related messages included:

```text
pam_unix(sshd:auth): authentication failure
```

```text
Accepted password for socadmin from 192.168.1.10
```

```text
Failed password for socadmin from 192.168.1.10
```

These events provide endpoint-side evidence of the controlled credential-testing and valid-account SSH activity.

Important investigation attributes include:

```text
Source IP
Target username
Authentication result
SSH process
Target host
Source port
Timestamp
```

---

## 5. Linux Journal

The Linux journal provided endpoint-level historical evidence for the SSH activity.

The SSH service journal was examined using:

```bash
sudo journalctl -u ssh
```

The Project 02 attack window contained authentication and session-related SSH events originating from:

```text
192.168.1.10
```

against:

```text
socadmin
```

The journal provided an independent endpoint-side source for correlating the Elastic telemetry.

---

## 6. `/var/log/auth.log`

The Linux authentication log provided additional endpoint evidence.

The Project 02 activity produced entries including:

```text
Accepted password for socadmin from 192.168.1.10
```

```text
pam_unix(sshd:session): session opened for user socadmin
```

```text
pam_unix(sshd:session): session closed for user socadmin
```

and authentication failures associated with:

```text
192.168.1.10
```

The authentication log was used to correlate SSH authentication activity with the corresponding Elastic events.

---

## 7. Successful SSH Authentication

A successful SSH authentication event was observed in Elastic at approximately:

```text
2026-10-02 08:36:22 IST
```

Relevant event information included:

```text
event.action:
ssh_login

process.name:
sshd

host.name:
soc-linux

source.ip:
192.168.1.10

source.port:
40598

user.name:
socadmin

data_stream.dataset:
system.auth
```

The corresponding authentication message was:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

This event provides direct correlation between the controlled attacker source, the valid account, and the SSH service.

---

## 8. Authentication Failure Telemetry

Elastic also recorded an authentication failure associated with the controlled activity.

Observed fields included:

```text
event.action:
authentication_failure

process.name:
sshd

host.name:
soc-linux

source.ip:
192.168.1.10

user.name:
socadmin
```

The authentication failure is important because it provides context around the credential-testing phase of the controlled attack.

---

## 9. Authentication-State Telemetry

Elastic also exposed authentication-state events.

The investigation identified:

```text
event.action:
authenticated
```

The event was associated with:

```text
host.name:
soc-linux
```

and the SSH authentication activity.

These events provide an additional authentication-state signal for threat hunting and correlation.

---

## 10. SSH Session Telemetry

Project 02 generated SSH session lifecycle telemetry.

Observed event types included:

```text
started-session
ended-session
logged-in
logged-off
```

Additional SSH-related events observed during the investigation included:

```text
was-authorized
acquired-credentials
```

These events provide additional context around the authentication and session lifecycle.

---

## 11. Elastic Agent

Elastic Agent operates on:

```text
Host:
soc-linux

IP:
192.168.1.16

Agent:
Elastic Agent
```

The agent was verified as:

```text
fleet
└─ status: (HEALTHY) Connected

elastic-agent
└─ status: (HEALTHY) Running
```

Elastic Agent provides the endpoint telemetry collection and forwarding layer between `soc-linux` and the Elastic environment.

---

## 12. Fleet Server

Fleet Server operates on:

```text
Host:
elastic-siem

IP:
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

Fleet Server health was validated during Project 02 environment verification.

The Fleet Server API returned:

```json
{
  "name": "fleet-server",
  "status": "HEALTHY"
}
```

---

## 13. Elastic SIEM

Elastic SIEM receives and normalizes the endpoint telemetry used for Project 02 investigation.

The SIEM environment is:

```text
Hostname:
elastic-siem

IP:
192.168.1.11
```

Kibana provides the investigation interface used to:

* Search authentication events
* Correlate source IPs
* Investigate user activity
* Inspect SSH events
* Review session telemetry
* Perform KQL hunting

---

## 14. Structured ECS Fields

The Project 02 investigation identified the following useful fields from actual Elastic events:

| Field                 | Purpose                      | Observed Value                                         |
| --------------------- | ---------------------------- | ------------------------------------------------------ |
| `source.ip`           | Origin of SSH activity       | `192.168.1.10`                                         |
| `source.port`         | Source connection port       | `40598`                                                |
| `user.name`           | Target account               | `socadmin`                                             |
| `event.action`        | Authentication/session state | `ssh_login`, `authentication_failure`, `authenticated` |
| `process.name`        | SSH process                  | `sshd`                                                 |
| `host.name`           | Target endpoint              | `soc-linux`                                            |
| `data_stream.dataset` | Event dataset                | `system.auth`                                          |

The investigation methodology uses fields observed in the actual events rather than assuming that a field exists before validating it.

---

## 15. Auditd

Auditd was enabled on the Linux endpoint before Project 02.

Baseline status included:

```text
enabled = 1
lost    = 0
backlog = 0
```

Auditd provides additional Linux security telemetry.

For Project 02, the primary evidence for the SSH valid-account activity came from SSH/system authentication telemetry and Elastic SSH events.

Auditd remains a supporting telemetry source for future investigations involving:

* Process execution
* Privilege activity
* File activity
* Persistence
* Suspicious shell behavior

---

## 16. Source-to-Field Relationship

```text
SSH / sshd
    │
    ├── Source IP
    │      └── source.ip
    │
    ├── Source Port
    │      └── source.port
    │
    ├── Target Account
    │      └── user.name
    │
    ├── Authentication / Session State
    │      └── event.action
    │
    ├── SSH Process
    │      └── process.name
    │
    └── Target Endpoint
           └── host.name
```

This field relationship provides the foundation for Project 02 threat hunting and detection engineering.

---

## 17. Threat-Hunting Sources

Project 02 hunting uses the following telemetry sources:

```text
Linux SSH / sshd
        │
        ├── Authentication events
        │
        ├── SSH login events
        │
        ├── Authentication-state events
        │
        └── Session lifecycle events
                │
                ▼
          Elastic Agent
                │
                ▼
             Elastic
                │
                ▼
             Kibana
                │
                ▼
          KQL Threat Hunt
```

The primary investigation pivots are:

```text
source.ip
user.name
host.name
event.action
process.name
```

---

## 18. Detection-Relevant Source

The observed Project 02 telemetry provides detection opportunities around:

```text
Successful SSH authentication
+
External source IP
+
Valid account
+
Authentication failures
+
SSH session activity
```

A detection can use these fields to identify suspicious valid-account SSH activity.

Detection logic must be based on the actual event fields observed in Elastic.

---

## 19. Evidence Hierarchy

Project 02 uses multiple sources for independent correlation.

```text
Level 1
Attack Tool
    │
    ▼
Credential-Acquisition / SSH Activity
    │
    ▼
Level 2
Endpoint SSH Logs
    │
    ▼
Level 3
Elastic SIEM Events
    │
    ▼
Level 4
Correlated Authentication / Session Events
    │
    ▼
Level 5
Threat-Hunting Timeline
```

This approach avoids relying on a single telemetry source.

---

## 20. Source Correlation

| Source              | Evidence                                     |
| ------------------- | -------------------------------------------- |
| Kali                | Controlled credential-testing / SSH activity |
| `sshd`              | Authentication and SSH session activity      |
| Linux journal       | Endpoint SSH authentication timeline         |
| `/var/log/auth.log` | Authentication and session evidence          |
| Elastic Agent       | Endpoint telemetry collection                |
| Fleet Server        | Agent management and connectivity            |
| Elastic SIEM        | Centralized event analysis                   |
| Kibana              | Event investigation                          |
| Auditd              | Supporting Linux security telemetry          |

---

## 21. Time Correlation

All three lab systems were standardized to:

```text
Asia/Kolkata
IST
UTC+05:30
```

Therefore, Project 02 investigation timestamps are documented in IST.

Elastic event timestamps are stored internally in UTC and displayed according to the investigation timezone.

The Project 02 attack window included observed activity around:

```text
08:36:22 IST
```

The underlying Linux authentication logs correspond to UTC timestamps.

Timezone normalization is required when correlating:

```text
Kali terminal
        +
Linux journal
        +
/var/log/auth.log
        +
Elastic events
        +
Kibana investigation
```

---

## 22. Primary vs Supporting Sources

### Primary

```text
sshd / Linux authentication telemetry
```

This directly records the SSH authentication activity occurring on the target endpoint.

### Supporting

```text
Kali attack activity
Linux journal
/var/log/auth.log
Elastic Agent
Fleet Server
Elastic SIEM
Kibana
Auditd
```

These sources provide execution context, endpoint evidence, centralized visibility, and correlation.

---

## 23. Project 02 Log-Source Matrix

| Source                  | Host           | Role                   | Project 02 Usage                        |
| ----------------------- | -------------- | ---------------------- | --------------------------------------- |
| Credential-testing tool | Kali           | Attack execution       | Controlled credential testing           |
| SSH                     | `soc-linux`    | Remote access service  | Authentication evidence                 |
| `sshd`                  | `soc-linux`    | SSH daemon             | Primary authentication/session evidence |
| Linux journal           | `soc-linux`    | Endpoint logging       | SSH activity timeline                   |
| `/var/log/auth.log`     | `soc-linux`    | Authentication logging | Authentication/session evidence         |
| Auditd                  | `soc-linux`    | Security auditing      | Supporting telemetry                    |
| Elastic Agent           | `soc-linux`    | Collection             | Endpoint-to-Fleet telemetry             |
| Fleet Server            | `elastic-siem` | Agent management       | Connectivity validation                 |
| Elastic SIEM            | `elastic-siem` | Central analysis       | Hunting and detection                   |
| Kibana                  | `elastic-siem` | Investigation          | Event analysis                          |

---

## 24. Log-Source Validation Checklist

* [x] SSH identified as the primary Project 02 source
* [x] `sshd` authentication activity confirmed
* [x] Linux journal confirmed
* [x] `/var/log/auth.log` confirmed
* [x] Source IP identified
* [x] Target account identified
* [x] Target host identified
* [x] Successful SSH authentication identified
* [x] Authentication failure identified
* [x] SSH session telemetry identified
* [x] Elastic Agent confirmed healthy
* [x] Fleet Server confirmed healthy
* [x] Elastic SIEM confirmed as analysis layer
* [x] ECS fields identified from actual events
* [x] Auditd identified as supporting Linux telemetry
* [x] Timezone normalization documented
* [x] Source correlation documented

---

## 25. Log-Source Conclusion

Project 02 established a complete telemetry chain:

```text
Kali
192.168.1.10
      │
      │ Credential Testing / SSH
      ▼
sshd
      │
      ├── Authentication Failure
      │
      ├── Successful Authentication
      │
      └── SSH Session Activity
              │
              ▼
       Linux Authentication Logs
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
              ├── Detection Engineering
              │
              └── Investigation
```




[1]: https://github.com/KdMenon007/CatchMe-Linux-SOC/blob/main/01-SSH-Brute-Force/02-telemetry/log-sources.md "CatchMe-Linux-SOC/01-SSH-Brute-Force/02-telemetry/log-sources.md at main · KdMenon007/CatchMe-Linux-SOC · GitHub"
