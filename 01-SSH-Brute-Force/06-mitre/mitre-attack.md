# Cyber Kill Chain Mapping

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Scenario:** SSH Brute Force Detection  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Target Service:** SSH (`TCP/22`)

---

## 1. Mapping Objective

This document maps the observed Project 01 activity to the Lockheed Martin Cyber Kill Chain phases.

The mapping is based only on activity actually demonstrated during the controlled lab simulation.

Because the password-guessing attack did not result in successful authentication, later attack phases were not demonstrated.

---

# 2. Cyber Kill Chain Summary

| Kill Chain Phase | Project 01 Activity | Status |
|---|---|---|
| Reconnaissance | SSH service on TCP/22 was identified during lab preparation | Observed |
| Weaponization | Controlled password list prepared for the simulation | Observed |
| Delivery | SSH authentication requests sent from Kali to Linux | Observed |
| Exploitation | Repeated password attempts against `socadmin` | Observed |
| Installation | No persistence or malware installation occurred | Not Observed |
| Command & Control | No C2 channel was established | Not Observed |
| Actions on Objectives | No successful compromise or post-authentication activity occurred | Not Observed |

---

# 3. Phase 1 — Reconnaissance

## Observed Activity

Before the attack, the Linux endpoint was verified as exposing SSH on TCP/22.

Target:

```text
soc-linux
192.168.1.16
```

Service:

```text
SSH
TCP/22
```

The attacker-side connectivity check confirmed that the SSH service was reachable from:

```text
192.168.1.10
```

## Status

**Observed**

This represents lab reconnaissance/service identification.

No external reconnaissance was performed.

---

# 4. Phase 2 — Weaponization

## Observed Activity

A controlled password list containing intentionally incorrect passwords was prepared for the SSH brute-force simulation.

The list was used by Hydra during the controlled attack.

The objective was to generate realistic authentication-failure telemetry for SOC detection testing.

## Status

**Observed**

No malware or exploit payload was created.

---

# 5. Phase 3 — Delivery

## Observed Activity

The attacker machine initiated SSH authentication traffic toward:

```text
192.168.1.16:22
```

Source:

```text
192.168.1.10
```

The Linux endpoint received the authentication requests through its SSH service.

Linux telemetry recorded authentication activity originating from the Kali system.

## Status

**Observed**

---

# 6. Phase 4 — Exploitation

## Observed Activity

Hydra performed repeated password attempts against:

```text
user: socadmin
service: SSH
target: 192.168.1.16
```

Linux SSH telemetry recorded repeated authentication failures.

Elastic recorded matching events containing:

```text
event.action = authentication_failure
process.name = sshd
host.name = soc-linux
source.ip = 192.168.1.10
user.name = socadmin
```

The controlled Hydra run reported:

```text
0 valid password found
```

Therefore, the attempted authentication attack was detected, but successful credential compromise was not demonstrated.

## Status

**Observed — Failed Authentication**

---

# 7. Phase 5 — Installation

## Expected Activity in a Successful Compromise

An attacker who successfully compromised the endpoint could potentially attempt to establish persistence or install malicious tooling.

## Project 01 Evidence

No installation or persistence activity was demonstrated.

No malware was installed.

No persistence mechanism was created.

## Status

**Not Observed**

---

# 8. Phase 6 — Command & Control

## Expected Activity in a Successful Compromise

A compromised endpoint could potentially communicate with attacker-controlled infrastructure.

## Project 01 Evidence

No command-and-control channel was established.

No post-authentication attacker session was demonstrated.

## Status

**Not Observed**

---

# 9. Phase 7 — Actions on Objectives

## Expected Activity in a Successful Compromise

This phase could involve activities such as:

* data access
* credential access
* collection
* exfiltration
* system modification
* destructive actions

## Project 01 Evidence

The attack did not progress to a successful authenticated session.

No post-compromise objective activity was demonstrated.

## Status

**Not Observed**

---

# 10. End-to-End Kill Chain

```text
┌─────────────────────┐
│ 1. RECONNAISSANCE   │
│ SSH TCP/22 identified│
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ 2. WEAPONIZATION    │
│ Controlled password │
│ list prepared       │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ 3. DELIVERY         │
│ SSH requests        │
│ Kali → Linux        │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│ 4. EXPLOITATION     │
│ Password guessing   │
│ Authentication fail │
└──────────┬──────────┘
           ↓
       ATTACK STOPPED
           │
           ├───────────────X Installation
           ├───────────────X Command & Control
           └───────────────X Actions on Objectives
```

---

# 11. SOC Detection Overlay

The Cyber Kill Chain activity generated endpoint telemetry that was processed through the SOC pipeline.

```text
Kali
192.168.1.10
     │
     │ SSH password attempts
     ▼
soc-linux
192.168.1.16
     │
     │ sshd authentication failures
     ▼
Elastic Agent
     │
     ▼
Elastic SIEM
     │
     ▼
KQL Threat Hunt
     │
     ▼
Detection Rule
     │
     │ source.ip aggregation
     │ threshold >= 3
     ▼
Medium Alert
     │
     ▼
SOC Investigation
     │
     ▼
MITRE ATT&CK Mapping
     │
     ▼
Incident Response
```

---

# 12. Kill Chain vs MITRE ATT&CK

| Cyber Kill Chain      | MITRE ATT&CK Context          | Project Evidence                 |
| --------------------- | ----------------------------- | -------------------------------- |
| Reconnaissance        | Service identification        | SSH/TCP 22 identified            |
| Weaponization         | Password guessing preparation | Controlled password list         |
| Delivery              | Remote Services: SSH          | SSH traffic to target            |
| Exploitation          | T1110.001 Password Guessing   | Repeated authentication failures |
| Installation          | No technique demonstrated     | Not observed                     |
| Command & Control     | No technique demonstrated     | Not observed                     |
| Actions on Objectives | No technique demonstrated     | Not observed                     |

---

# 13. Evidence Correlation

| Evidence Source              | Observation                                          |
| ---------------------------- | ---------------------------------------------------- |
| Kali                         | Hydra generated repeated SSH authentication attempts |
| Linux SSH                    | Repeated authentication failures from `192.168.1.10` |
| Elastic telemetry            | `authentication_failure` events                      |
| Threat hunting               | Source IP correlated with target host                |
| Detection rule               | Threshold reached at 4 matching events               |
| Elastic Alert                | Medium severity alert generated                      |
| Investigation                | Events correlated to the same attack window          |
| Authentication result        | `0 valid password found`                             |
| Post-authentication activity | Not demonstrated                                     |

---

# 14. Attack Boundary

The demonstrated attack chain ends at the authentication stage.

```text
Recon
  ↓
Weaponization
  ↓
Delivery
  ↓
Password Guessing
  ↓
Authentication Failure
  ↓
SOC Detection
  ↓
Investigation
```

There is no evidence supporting continuation into:

```text
Installation
Command & Control
Actions on Objectives
```

---

# 15. Detection Point

The primary SOC detection point occurred during the exploitation phase.

Observed behavior:

```text
Multiple SSH authentication failures
        ↓
Same source IP
        ↓
Same target host
        ↓
Same SSH process
        ↓
Threshold reached
        ↓
Elastic alert
```

Detection configuration:

```text
Rule:
CatchMe - Linux SSH Brute Force Detection

Condition:
event.action = authentication_failure
AND process.name = sshd
AND host.name = soc-linux

Aggregation:
source.ip

Threshold:
>= 3

Observed matching events:
4

Severity:
Medium

Risk Score:
47
```

---

# 16. Incident Response Transition

The failed authentication attack still produced a complete SOC investigation workflow:

```text
Attack
  ↓
Telemetry
  ↓
Threat Hunting
  ↓
Detection
  ↓
Alert
  ↓
Investigation
  ↓
MITRE Mapping
  ↓
Kill Chain Mapping
  ↓
Containment Recommendations
  ↓
Remediation Recommendations
  ↓
Lessons Learned
```

---

# 17. Evidence References

```text
screenshots/attack/02-kali-hydra-detection-test.png

screenshots/telemetry/01-elastic-ssh-authentication-events.png

screenshots/hunting/01-kql-authentication-failures.png
screenshots/hunting/02-kql-sshd-source-correlation.png

screenshots/detection/05-brute-force-alert-generated.png

screenshots/investigation/01-alert-overview.png
screenshots/investigation/04-source-events-correlation.png
screenshots/investigation/05-linux-ssh-journal-current-attack.png
```

---

# 18. Final Assessment

Project 01 demonstrated the following Cyber Kill Chain progression:

```text
RECONNAISSANCE
      ↓
WEAPONIZATION
      ↓
DELIVERY
      ↓
EXPLOITATION
      ↓
DETECTION
      ↓
INVESTIGATION
```

The attack did not progress to installation, command and control, or actions on objectives.

The controlled simulation therefore represents a **detected SSH password-guessing attempt without demonstrated successful compromise**.

---

# 19. Project 01 Status

| Component                        | Status   |
| -------------------------------- | -------- |
| Reconnaissance mapping           | Complete |
| Weaponization mapping            | Complete |
| Delivery mapping                 | Complete |
| Exploitation mapping             | Complete |
| Installation assessment          | Complete |
| C2 assessment                    | Complete |
| Actions-on-objectives assessment | Complete |
| SOC detection correlation        | Complete |
| MITRE ATT&CK mapping             | Complete |
| Cyber Kill Chain mapping         | Complete |

