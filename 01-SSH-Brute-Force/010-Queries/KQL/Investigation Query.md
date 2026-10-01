# Investigation Query

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Incident ID:** `CATCHME-LNX-001`  
**Scenario:** SSH Brute Force Detection  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Service:** SSH (`TCP/22`)

---

# 1. Query Objective

This document describes the KQL query used during the final investigation to isolate the SSH authentication-failure events associated with the detection-validation attack.

The query was used to correlate the Elastic alert with the underlying authentication events and confirm the activity observed during the investigation window.

---

# 2. Investigation Query

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```
---

# 3. Investigation Time Window

The investigation was narrowed to:

```text
08:55 IST → 09:00 IST
```

This time window was used to isolate the detection-validation attack from the earlier Project 01 attack test.

---

# 4. Query Breakdown

### Authentication Failure

```kql id="r4z9q1"
event.action : "authentication_failure"
```

Identifies failed authentication events.

### Source IP

```kql id="x1y5se"
source.ip : "192.168.1.10"
```

Restricts the investigation to the controlled Kali attacker source.

### Target Host

```kql id="m0k3zv"
host.name : "soc-linux"
```

Restricts the results to the Linux SOC endpoint.

---

# 5. Investigation Logic

```text
┌──────────────────────────────┐
│ Elastic Authentication Events│
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Authentication Failure       │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Source = 192.168.1.10        │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Host = soc-linux              │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Narrow Investigation Window  │
│ 08:55 → 09:00 IST            │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Correlate With Alert         │
└──────────────────────────────┘
```

---

# 6. Observed Events

The query identified four relevant authentication-failure events within the investigation window.

The events contained:

| Field          | Observed Value           |
| -------------- | ------------------------ |
| `source.ip`    | `192.168.1.10`           |
| `user.name`    | `socadmin`               |
| `event.action` | `authentication_failure` |
| `process.name` | `sshd`                   |
| `host.name`    | `soc-linux`              |

---

# 7. Alert Correlation

The investigation results were correlated with the generated Elastic detection alert.

The alert contained:

```text
Rule:
CatchMe - Linux SSH Brute Force Detection

Threshold Count:
4

Source IP:
192.168.1.10

Severity:
Medium

Risk Score:
47

Status:
Open
```

The four matching authentication-failure events therefore provided the underlying event evidence for the threshold alert.

---

# 8. Timeline Correlation

The investigation query results were correlated with the Linux SSH journal and Kali attack execution.

```text
08:57:17 IST
      ↓
Hydra attack begins
      ↓
SSH authentication failures
      ↓
Elastic authentication events
      ↓
08:57:26.518 IST
      ↓
Original event associated with detection
      ↓
08:58:02.247 IST
      ↓
Elastic detection alert generated
```

The Linux endpoint records time in UTC, so the corresponding endpoint activity began at approximately:

```text
03:27:17 UTC
=
08:57:17 IST
```

---

# 9. Investigation Correlation

```text
Kali
192.168.1.10
     ↓
Hydra
     ↓
SSH / TCP 22
     ↓
soc-linux
192.168.1.16
     ↓
sshd
     ↓
Authentication Failures
     ↓
Elastic SIEM
     ↓
Investigation KQL
     ↓
4 Matching Events
     ↓
Detection Alert
```

---

# 10. Investigation Outcome

The query successfully isolated the authentication failures associated with the detection-validation attack.

The investigation established:

```text
Source       : 192.168.1.10
Target       : soc-linux
Target IP    : 192.168.1.16
Account      : socadmin
Process      : sshd
Event        : authentication_failure
Event Count  : 4
```

The results were consistent with the controlled SSH password-guessing activity.

---

# 11. Evidence

Related investigation evidence is available under:

```text
screenshots/
└── investigation/
    ├── 01-alert-overview.png
    ├── 02-alert-table-metadata.png
    ├── 04-source-events-correlation.png
    └── 05-linux-ssh-journal-current-attack.png
```

