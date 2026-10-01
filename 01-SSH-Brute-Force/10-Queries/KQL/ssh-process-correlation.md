# SSH Process Correlation

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

This document describes the KQL query used to correlate SSH process activity with the controlled attacker source and the affected Linux endpoint.

The query was used after the initial authentication-failure hunt to expand the investigation and identify SSH-related events associated with the attack source.

---

# 2. Correlation Query

```kql
process.name : "sshd" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```
---

# 3. Query Breakdown

### SSH Process Identification

```kql
process.name : "sshd"
```

Restricts the results to events associated with the SSH daemon process.

### Source Identification

```kql
source.ip : "192.168.1.10"
```

Restricts the results to activity originating from the controlled Kali attacker system.

### Target Identification

```kql
host.name : "soc-linux"
```

Restricts the results to the Linux SOC endpoint.

---

# 4. Correlation Logic

```text
┌──────────────────────────────┐
│ Elastic SSH-Related Events  │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ Process = sshd               │
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
│ Correlated SSH Activity       │
└──────────────────────────────┘
```

---

# 5. Investigation Fields

The resulting events were reviewed using:

| Field          | Purpose                        |
| -------------- | ------------------------------ |
| `@timestamp`   | Establish event timing         |
| `source.ip`    | Identify the source            |
| `user.name`    | Identify the targeted account  |
| `event.action` | Identify the event type        |
| `process.name` | Confirm SSH process activity   |
| `host.name`    | Identify the affected endpoint |

---

# 6. Observed Activity

The query returned SSH-related events associated with:

```text
Source IP      : 192.168.1.10
Target Host    : soc-linux
Target IP      : 192.168.1.16
Target Account : socadmin
Process        : sshd
```

The results included authentication-related activity generated during the controlled SSH password-guessing simulation.

---

# 7. Correlation With Authentication Failures

The SSH process query was used together with the primary authentication-failure query.

```text
Authentication Failure Hunt
          ↓
Identify Source IP
          ↓
SSH Process Correlation
          ↓
Confirm sshd Activity
          ↓
Correlate Host / User / Time
```

This provided additional context around the authentication-failure events identified during the initial hunt.

---

# 8. Attack Correlation

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
Authentication Activity
     ↓
Elastic Agent
     ↓
Elastic SIEM
     ↓
KQL Correlation
```

---

# 9. Investigation Outcome

The query provided supporting evidence that the authentication activity was associated with the `sshd` process on `soc-linux` and originated from the controlled Kali system.

This correlation was used to strengthen the investigation before proceeding to detection validation and alert analysis.

---

# 10. Evidence

Related hunting evidence is available under:

```text
screenshots/
└── hunting/
    └── 02-kql-sshd-source-correlation.png
```

---

# 11. Final Finding

The SSH process correlation query successfully linked the observed authentication activity to:

```text
Source  : 192.168.1.10
Host    : soc-linux
Process : sshd
User    : socadmin
```
