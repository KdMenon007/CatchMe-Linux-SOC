# Project 01 — Attack Simulation

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Scenario:** SSH Brute Force → Compromise  
**Actual Test:** Controlled SSH Password-Guessing Simulation  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Target Service:** SSH / TCP 22

---

## 1. Objective

The objective of this simulation was to generate controlled SSH authentication-failure telemetry against the CatchMe Linux SOC endpoint and validate the complete SOC workflow:

```text
Controlled Attack
        ↓
SSH Authentication Failures
        ↓
Linux Endpoint Telemetry
        ↓
Elastic Agent
        ↓
Elastic SIEM
        ↓
KQL Threat Hunting
        ↓
Detection Engineering
        ↓
Alert
        ↓
Investigation
```
The simulation was performed only against the dedicated CatchMe laboratory endpoint.

---

## 2. Lab Attack Path

```text
┌──────────────────────────────┐
│        Kali Attacker         │
│          kiran               │
│       192.168.1.10           │
└──────────────┬───────────────┘
               │
               │ SSH TCP/22
               │ Password Attempts
               ▼
┌──────────────────────────────┐
│       Linux Endpoint         │
│        soc-linux             │
│       192.168.1.16           │
│                              │
│          sshd                │
└──────────────┬───────────────┘
               │
               │ Telemetry
               ▼
┌──────────────────────────────┐
│        Elastic SIEM          │
│        elastic-siem          │
│       192.168.1.11           │
└──────────────────────────────┘
```

---

## 3. Pre-Attack Conditions

Before executing the attack:

* `soc-linux` was online.
* SSH was listening on TCP/22.
* Kali could reach `192.168.1.16:22`.
* Elastic Agent was healthy.
* Fleet connectivity was healthy.
* Auditd was enabled.
* No Project 01 persistence mechanism had been established.
* The target account was `socadmin`.

---

## 4. Controlled Password List

A temporary password list containing intentionally incorrect passwords was used for the simulation.

The passwords were created specifically for this isolated lab test.

The list contained:

```text
WrongPass01!
WrongPass02!
WrongPass03!
WrongPass04!
WrongPass05!
```

No real credentials were used.

---

## 5. Attack Execution

The controlled SSH password-guessing simulation was performed from:

```text
Kali: 192.168.1.10
```

against:

```text
Target: 192.168.1.16:22
Account: socadmin
```

The Hydra command used was:

```bash
hydra -l socadmin -P /tmp/project01-passwords.txt -t 2 -f ssh://192.168.1.16
```

### Attack Parameters

| Parameter                   | Value          |
| --------------------------- | -------------- |
| Tool                        | Hydra          |
| Protocol                    | SSH            |
| Target                      | `192.168.1.16` |
| Port                        | `22`           |
| Username                    | `socadmin`     |
| Password attempts           | 5              |
| Parallel tasks              | 2              |
| Stop after valid credential | Enabled        |
| Environment                 | CatchMe lab    |

---

## 6. Attack Execution Result

Hydra reported:

```text
1 of 1 target completed, 0 valid password found
```

The controlled attack therefore generated authentication failures but did **not** obtain valid credentials.

### Actual Outcome

```text
Password guessing activity
        ↓
Multiple SSH authentication failures
        ↓
No valid password
        ↓
No successful SSH authentication
        ↓
No demonstrated compromise
```

---

## 7. Attack Timing

The detection-validation attack began at approximately:

```text
08:57:17 IST
```

The corresponding Linux UTC time was:

```text
03:27:17 UTC
```

The attack completed at approximately:

```text
08:57:26 IST
```

The corresponding Linux UTC time was:

```text
03:27:26 UTC
```

---

## 8. Endpoint Evidence

During the attack, `soc-linux` recorded SSH authentication failures originating from:

```text
192.168.1.10
```

against:

```text
user=socadmin
```

The SSH process was:

```text
sshd
```

Observed activity included:

```text
pam_unix(sshd:auth): authentication failure
```

and:

```text
Failed password for socadmin from 192.168.1.10
```

The connections subsequently closed during pre-authentication.

---

## 9. Elastic Telemetry

The attack generated SSH authentication-failure events that were collected by Elastic Agent and made available in Elastic SIEM.

The investigation query used later was:

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```

Within the isolated detection-validation window, the query returned:

```text
4 matching events
```

The relevant fields were:

| Field          | Observed Value           |
| -------------- | ------------------------ |
| `source.ip`    | `192.168.1.10`           |
| `user.name`    | `socadmin`               |
| `event.action` | `authentication_failure` |
| `process.name` | `sshd`                   |
| `host.name`    | `soc-linux`              |

---

## 10. Attack → Telemetry Correlation

The observed sequence was:

```text
Kali
192.168.1.10
        │
        │ Hydra SSH attempts
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
        │ 4 matching events
        ▼
KQL Investigation
        │
        ▼
Detection Rule
        │
        ▼
Medium Severity Alert
```

---

## 11. Successful Compromise Assessment

### Credential Compromise

**Not demonstrated**

Hydra reported:

```text
0 valid password found
```

### Successful SSH Login

**Not observed**

### Post-Authentication Activity

**Not observed**

### Privilege Escalation

**Not observed**

### Persistence

**Not observed**

### Host Compromise

**Not demonstrated**

---

## 12. Evidence Integrity

The Project 01 investigation distinguishes between:

### Earlier Lab Activity

An earlier test occurred around:

```text
08:29 IST
```

### Detection-Validation Attack

The detection-validation attack occurred around:

```text
08:57 IST
```

The investigation window was narrowed to:

```text
08:55–09:00 IST
```

This isolated the four authentication-failure events associated with the detection-validation run.

Existing legitimate administrative SSH activity from `192.168.1.10` was not attributed to Hydra.

---

## 13. Screenshots

Capture and store the following evidence:

```text
screenshots/
└── attack/
    ├── 01-kali-pre-attack-connectivity.png
    └── 02-kali-hydra-detection-test.png
```

### Screenshot 01

Show:

* Kali hostname
* Kali IP
* Target IP
* TCP/22 connectivity

### Screenshot 02

Show:

* Hydra command
* Target
* Attack timing
* Number of attempts
* `0 valid password found`

---

## 14. Attack Evidence Checklist

* [x] Dedicated lab target confirmed
* [x] Attacker IP confirmed
* [x] Target IP confirmed
* [x] SSH TCP/22 confirmed
* [x] Controlled password list created
* [x] Hydra executed
* [x] Authentication failures generated
* [x] Linux SSH telemetry observed
* [x] Elastic telemetry observed
* [x] Four detection-test events correlated
* [x] No valid password obtained
* [x] No successful compromise demonstrated

---

## 15. Final Attack Finding

The Project 01 attack successfully simulated repeated SSH password-guessing activity against the controlled Linux endpoint.

The actual evidence demonstrates:

```text
192.168.1.10
      │
      │ SSH password guessing
      ▼
192.168.1.16
      │
      │ authentication failures
      ▼
Elastic SIEM
      │
      │ 4 matching events
      ▼
Detection
      │
      ▼
Alert
```

The attack generated the telemetry required for SOC detection and investigation.

However:

```text
0 valid password found
```

Therefore, this run must be documented as a **detected SSH brute-force/password-guessing attempt with no demonstrated compromise**, rather than a confirmed successful SSH compromise.

---

