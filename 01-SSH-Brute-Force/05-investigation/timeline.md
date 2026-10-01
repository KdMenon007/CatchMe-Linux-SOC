# MITRE ATT&CK Mapping

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Scenario:** SSH Brute Force Detection  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Target Service:** SSH (`TCP/22`)

---

## 1. Mapping Objective

This document maps the observed attack behavior to the relevant MITRE ATT&CK techniques.

The mapping is based only on behavior demonstrated during the controlled Project 01 simulation and the telemetry collected from the Linux endpoint and Elastic SIEM.

The strongest mapping is:

**T1110.001 — Brute Force: Password Guessing**

SSH is also relevant as the remote service targeted by the password-guessing activity.

---

## 2. Technique Summary

| Technique ID | Technique | Tactic | Evidence | Status |
|---|---|---|---|---|
| **T1110.001** | Brute Force: Password Guessing | Credential Access | Repeated SSH password attempts against `socadmin`; multiple authentication failures from `192.168.1.10` | **Observed** |
| **T1021.004** | Remote Services: SSH | Lateral Movement | SSH on TCP/22 was the service targeted by the password-guessing activity | **Supporting Context** |

---

# 3. T1110.001 — Brute Force: Password Guessing

## ATT&CK Context

**Technique:** T1110 — Brute Force  
**Sub-technique:** T1110.001 — Password Guessing  
**Tactic:** Credential Access

The Project 01 simulation repeatedly attempted passwords against the Linux `socadmin` account through SSH.

The controlled attack was performed against the user's own lab endpoint.

---

## Observed Attack

### Source

```text
Kali attacker
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

### Attack Tool

```text
Hydra
```

### Password Attempts

The controlled password list contained five intentionally incorrect passwords.

Hydra reported:

```text
0 valid password found
```

---

## Endpoint Evidence

Linux SSH telemetry recorded repeated authentication failures originating from:

```text
192.168.1.10
```

The observed authentication events contained:

```text
event.action = authentication_failure
process.name = sshd
host.name = soc-linux
source.ip = 192.168.1.10
user.name = socadmin
```

The Linux SSH journal also recorded repeated failed password attempts from the same source address.

---

## Elastic Detection Evidence

The detection rule used:

```kql
event.action : "authentication_failure"
and process.name : "sshd"
and host.name : "soc-linux"
```

Events were aggregated by:

```text
source.ip
```

Threshold:

```text
>= 3
```

The controlled detection test generated:

```text
4 matching events
```

The resulting alert identified:

```text
source.ip = 192.168.1.10
```

---

## Assessment

**T1110.001 — Observed**

The available evidence directly demonstrates repeated password-guessing activity against the SSH authentication interface.

The simulation did **not** demonstrate successful credential compromise.

---

# 4. T1021.004 — Remote Services: SSH

## ATT&CK Context

**Technique:** T1021 — Remote Services
**Sub-technique:** T1021.004 — SSH
**Tactic:** Lateral Movement

SSH was the remote service targeted during the password-guessing simulation.

---

## Observed Service

```text
Service: SSH
Protocol: TCP
Port: 22
Process: sshd
Target: 192.168.1.16
```

The Linux endpoint confirmed that SSH was listening on TCP/22 before the attack.

---

## Evidence

The Elastic events associated with the attack included:

```text
process.name = sshd
host.name = soc-linux
source.ip = 192.168.1.10
```

The endpoint SSH journal recorded authentication activity from the Kali attacker.

---

## Assessment

**T1021.004 — Supporting Context**

SSH was the remote service targeted by the password-guessing activity.

However, the project did **not** demonstrate successful remote access through SSH.

Therefore, this mapping should not be interpreted as evidence of successful lateral movement.

---

# 5. Techniques Not Mapped

## T1078 — Valid Accounts

**Status:** Not observed

The simulation did not successfully authenticate using a valid credential.

Hydra reported:

```text
0 valid password found
```

No evidence was collected demonstrating successful use of valid credentials.

Therefore, T1078 is not mapped to the observed attack.

---

## Successful SSH Remote Access

Successful SSH access was not demonstrated during the attack.

The observed activity consisted of authentication failures and connection termination during the pre-authentication stage.

No post-authentication shell activity was demonstrated as part of this attack.

---

## Post-Compromise Techniques

The following were not mapped because the attack did not reach a successful authenticated session:

```text
Privilege Escalation
Persistence
Command Execution After Login
Lateral Movement
Collection
Exfiltration
Impact
```

The project therefore represents a **failed credential-access attempt with successful SOC detection**, rather than a successful host compromise.

---

# 6. MITRE Evidence Chain

```text
Kali
192.168.1.10
        |
        | Repeated SSH password attempts
        v
SSH
TCP/22
        |
        v
soc-linux
192.168.1.16
        |
        | authentication_failure
        v
Elastic Agent
        |
        v
Elastic SIEM
        |
        | source.ip aggregation
        | threshold >= 3
        v
Detection Rule
        |
        v
Medium Severity Alert
        |
        v
SOC Investigation
```

---

# 7. Detection Relationship

The observed ATT&CK behavior was converted into a detection model.

### Detection Query

```kql
event.action : "authentication_failure"
and process.name : "sshd"
and host.name : "soc-linux"
```

### Aggregation

```text
Group by: source.ip
```

### Threshold

```text
3 or more authentication failures
```

### Detection Result

```text
Source IP: 192.168.1.10
Matching events: 4
Severity: Medium
Risk score: 47
Status: Open
```

This provided the transition from:

```text
Observed ATT&CK behavior
        ↓
Threat Hunting
        ↓
Detection Engineering
        ↓
Alert
        ↓
Investigation
```

---

# 8. MITRE ATT&CK Mapping Diagram

```text
                    PROJECT 01
               SSH BRUTE FORCE
                       |
                       v
             ┌────────────────────┐
             │ Credential Access  │
             └─────────┬──────────┘
                       |
                       v
             ┌────────────────────┐
             │ T1110.001         │
             │ Password Guessing  │
             └─────────┬──────────┘
                       |
                       | SSH targeted
                       v
             ┌────────────────────┐
             │ T1021.004         │
             │ Remote Services    │
             │ SSH                │
             └────────────────────┘
```

---

# 9. ATT&CK-to-Evidence Matrix

| ATT&CK Element               | Observed Evidence                     |
| ---------------------------- | ------------------------------------- |
| Password guessing            | Multiple failed SSH password attempts |
| Source                       | `192.168.1.10`                        |
| Target                       | `192.168.1.16`                        |
| Account                      | `socadmin`                            |
| Service                      | SSH                                   |
| Process                      | `sshd`                                |
| Authentication result        | Failure                               |
| Elastic event action         | `authentication_failure`              |
| Detection threshold          | `>= 3`                                |
| Detection result             | 4 matching events                     |
| Alert                        | Medium severity                       |
| Successful credential use    | Not demonstrated                      |
| Successful SSH session       | Not demonstrated                      |
| Post-authentication activity | Not demonstrated                      |

---

# 10. Evidence References

```text
screenshots/attack/02-kali-hydra-detection-test.png

screenshots/telemetry/01-elastic-ssh-authentication-events.png

screenshots/hunting/01-kql-authentication-failures.png
screenshots/hunting/02-kql-sshd-source-correlation.png

screenshots/detection/05-brute-force-alert-generated.png

screenshots/investigation/01-alert-overview.png
screenshots/investigation/02-alert-table-metadata.png
screenshots/investigation/04-source-events-correlation.png
screenshots/investigation/05-linux-ssh-journal-current-attack.png
```

---

# 11. Evidence Limitations

The collected evidence supports:

* repeated password-guessing activity
* SSH targeting
* source and destination correlation
* repeated authentication failures
* Elastic detection
* SOC alert generation
* investigation and timeline correlation

The evidence does **not** support claims of:

* successful password discovery
* successful account compromise
* successful SSH login during the attack
* privilege escalation
* persistence
* lateral movement
* command execution after authentication
* data access
* data exfiltration
* impact

---

# 12. Final MITRE Assessment

## Primary Technique

**T1110.001 — Brute Force: Password Guessing**

**Status:** Observed

The evidence directly demonstrates repeated password-guessing attempts against the `socadmin` SSH account.

## Supporting Technique

**T1021.004 — Remote Services: SSH**

**Status:** Supporting Context

SSH was the remote service targeted during the password-guessing activity.

## Overall Result

```text
Credential Access Attempt
        ↓
Password Guessing
        ↓
SSH Target
        ↓
Authentication Failure
        ↓
Elastic Telemetry
        ↓
KQL Hunting
        ↓
Detection Rule
        ↓
Alert
        ↓
SOC Investigation
```

The Project 01 simulation successfully demonstrated the **detection and investigation of SSH password-guessing activity**, while no successful credential compromise was demonstrated.

---

# 13. Project 01 MITRE Status

| Stage                 | Status               |
| --------------------- | -------------------- |
| Attack simulation     | Complete             |
| Telemetry validation  | Complete             |
| Threat hunting        | Complete             |
| Detection engineering | Complete             |
| Alert generation      | Complete             |
| Investigation         | Complete             |
| Timeline correlation  | Complete             |
| MITRE ATT&CK mapping  | Complete             |
| Successful compromise | **Not demonstrated** |

