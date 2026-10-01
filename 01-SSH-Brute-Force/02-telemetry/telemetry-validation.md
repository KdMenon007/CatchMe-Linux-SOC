# Project 01 — Telemetry Validation

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Scenario:** SSH Brute Force → Compromise  
**Telemetry Validation:** SSH Authentication Failure Activity  
**Endpoint:** `soc-linux` (`192.168.1.16`)  
**SIEM:** `elastic-siem` (`192.168.1.11`)  
**Attacker:** Kali (`192.168.1.10`)

---

## 1. Objective

The objective of this stage is to verify that the controlled SSH password-guessing activity generated observable telemetry across the CatchMe Linux SOC pipeline.

The validation path is:

```text
SSH Attack
    ↓
sshd
    ↓
Linux Authentication Logs
    ↓
Elastic Agent
    ↓
Fleet
    ↓
Elastic SIEM
    ↓
Kibana Discover
```
The telemetry validation confirms that the attack activity can be observed before detection engineering is evaluated.

---

## 2. Telemetry Sources

Project 01 uses the following Linux telemetry sources:

| Source          | Purpose                               |
| --------------- | ------------------------------------- |
| `sshd`          | SSH authentication activity           |
| Linux journal   | Authentication and SSH service events |
| Elastic Agent   | Endpoint telemetry collection         |
| Auditd          | Linux security auditing               |
| Elastic SIEM    | Centralized telemetry analysis        |
| Kibana Discover | Event-level investigation             |

The project notes emphasize inspecting actual events and fields before assuming field names. 

---

# 3. SSH Telemetry

The SSH daemon generated authentication events during the controlled attack.

Observed source:

```text
sshd
```

Observed attacker:

```text
192.168.1.10
```

Observed target:

```text
192.168.1.16
```

Observed account:

```text
socadmin
```

The endpoint recorded events including:

```text
pam_unix(sshd:auth): authentication failure
```

and:

```text
Failed password for socadmin from 192.168.1.10
```

The SSH connections subsequently closed during pre-authentication.

---

# 4. Endpoint Telemetry Validation

The detection-validation attack occurred during:

```text
08:55–09:00 IST
```

The Linux endpoint uses UTC.

The relevant endpoint attack window was:

```text
03:27–03:30 UTC
```

The endpoint SSH journal recorded:

```text
03:27:17 UTC
03:27:19 UTC
03:27:22 UTC
03:27:23 UTC
03:27:25 UTC
03:27:26 UTC
```

These events correspond to the same attack window observed from Kali in IST.

---

# 5. Endpoint Authentication Evidence

The SSH journal showed authentication failures from:

```text
192.168.1.10
```

against:

```text
user=socadmin
```

Example event pattern:

```text
pam_unix(sshd:auth): authentication failure
```

followed by:

```text
Failed password for socadmin from 192.168.1.10
```

The final connection closed during pre-authentication.

This establishes that the endpoint itself observed the password-guessing activity before considering the SIEM layer.

---

# 6. Elastic Agent Validation

The Linux endpoint was running Elastic Agent:

```text
Elastic Agent: 9.5.2
```

Expected health state:

```text
fleet
└─ status: (HEALTHY) Connected

elastic-agent
└─ status: (HEALTHY) Running
```

Fleet Server:

```text
https://elastic-siem:8220
```

Elastic Agent therefore provided the transport layer between the Linux endpoint and the CatchMe SIEM environment.

---

# 7. Fleet Connectivity

The endpoint maintained communication with:

```text
elastic-siem
192.168.1.11
```

over:

```text
TCP/8220
```

Fleet Server health validation returned:

```text
HTTP 200
```

with:

```json
{
  "name": "fleet-server",
  "status": "HEALTHY"
}
```

This confirms that Fleet connectivity was available during the lab operation.

---

# 8. Elastic SIEM Validation

The SSH authentication telemetry was visible in Elastic SIEM.

The relevant investigation fields were:

| ECS Field      | Observed Value           |
| -------------- | ------------------------ |
| `source.ip`    | `192.168.1.10`           |
| `user.name`    | `socadmin`               |
| `event.action` | `authentication_failure` |
| `process.name` | `sshd`                   |
| `host.name`    | `soc-linux`              |

These structured fields provide the basis for the Project 01 threat hunt and detection.

---

# 9. KQL Telemetry Validation Query

The primary telemetry validation query was:

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```

The isolated detection-validation window returned:

```text
4 matching events
```

---

# 10. Event Correlation

The four matching events contained the following common values:

```text
source.ip    = 192.168.1.10
user.name    = socadmin
event.action = authentication_failure
process.name = sshd
host.name    = soc-linux
```

This establishes a consistent relationship between:

```text
Attacker
192.168.1.10
       ↓
SSH
       ↓
Target account
socadmin
       ↓
sshd
       ↓
Authentication failure
       ↓
soc-linux
192.168.1.16
```

---

# 11. Telemetry Pipeline

The complete observed telemetry path is:

```text
┌─────────────────────────┐
│     Kali Attacker       │
│      192.168.1.10      │
└────────────┬────────────┘
             │
             │ SSH authentication attempts
             ▼
┌─────────────────────────┐
│      soc-linux          │
│      192.168.1.16      │
│                         │
│         sshd            │
└────────────┬────────────┘
             │
             │ Authentication telemetry
             ▼
┌─────────────────────────┐
│     Elastic Agent       │
│        9.5.2            │
└────────────┬────────────┘
             │
             │ Fleet
             ▼
┌─────────────────────────┐
│      elastic-siem       │
│      192.168.1.11      │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│     Elastic SIEM        │
│      Kibana Discover    │
└─────────────────────────┘
```

---

# 12. Auditd Context

Auditd was enabled on the endpoint during the Project 01 baseline.

Baseline:

```text
enabled: 1
lost:    0
backlog: 0
```

Project 01's primary authentication evidence came from SSH/system authentication telemetry.

Auditd remains an important telemetry source for later Linux investigations involving:

* Process execution
* Account activity
* File activity
* Privilege activity
* Persistence
* Suspicious command execution

The project notes identify these as core Linux hunting areas. 

---

# 13. Telemetry Integrity

The investigation separates the detection-validation attack from earlier lab activity.

Earlier activity:

```text
~08:29 IST
```

Detection-validation activity:

```text
08:55–09:00 IST
```

Only the detection-validation window was used to establish the four-event correlation for this stage.

This prevents earlier legitimate or test activity from being incorrectly attributed to the final detection-validation run.

---

# 14. Telemetry → Hunting Relationship

The telemetry stage provides the evidence required for the next SOC stage:

```text
Raw Telemetry
      ↓
Identify Reliable Fields
      ↓
Build KQL Hunt
      ↓
Validate Event Pattern
      ↓
Correlate Source / User / Host / Process
      ↓
Detection Engineering
```

The project methodology specifically separates threat hunting from detection engineering: a hunting query is not automatically considered a production-quality detection. 

---

# 15. Screenshot Evidence

Store the telemetry evidence under:

```text
screenshots/
└── telemetry/
    └── 01-elastic-ssh-authentication-events.png
```

The screenshot should show:

* Kibana Discover
* Project 01 investigation window
* `source.ip`
* `user.name`
* `event.action`
* `process.name`
* `host.name`
* Four matching authentication-failure events

---

# 16. Telemetry Validation Checklist

* [x] SSH attack generated endpoint activity
* [x] `sshd` recorded authentication failures
* [x] Source IP identified
* [x] Target user identified
* [x] Target host identified
* [x] Elastic Agent healthy
* [x] Fleet connectivity confirmed
* [x] Elastic SIEM received telemetry
* [x] Structured ECS fields identified
* [x] KQL query returned matching events
* [x] Four events correlated in the isolated detection window
* [x] Earlier activity excluded from final validation window
* [x] No successful authentication observed

---

# 17. Telemetry Validation Finding

The Project 01 attack generated observable Linux SSH authentication telemetry.

The evidence chain is:

```text
Kali
192.168.1.10
      ↓
SSH password attempts
      ↓
sshd
      ↓
Authentication failures
      ↓
Elastic Agent
      ↓
Fleet
      ↓
Elastic SIEM
      ↓
4 matching events
```

The telemetry pipeline successfully provided the structured fields required for threat hunting and detection engineering.

---

