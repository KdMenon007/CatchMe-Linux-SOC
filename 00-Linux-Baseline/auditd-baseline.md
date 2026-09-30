# Auditd Baseline

## Purpose

This document records the baseline Linux Audit Framework state on the CatchMe Linux SOC lab endpoint before security attack simulations begin.

Auditd provides security-relevant telemetry for detecting and investigating:

- User activity
- Privilege escalation
- Command execution
- File and directory changes
- Authentication activity
- Permission changes
- Process activity
- Security configuration changes
- Persistence mechanisms
- Potential attacker behavior

## Lab Environment

| Field | Value |
|---|---|
| Hostname | `soc-linux` |
| IP Address | `192.168.56.20` |
| Operating System | Ubuntu 24.04.4 LTS |
| Architecture | `aarch64` |
| SIEM | `catchme-siem` |
| SIEM IP | `192.168.56.10` |
| Network | Private CatchMe Lab |

> The public version uses sanitized private lab addresses.

## Auditd Status

The audit subsystem baseline was collected using:

```bash
sudo auditctl -s
```

Baseline result:

```text
enabled 1
failure 1
pid 687
rate_limit 0
backlog_limit 8192
lost 0
backlog 0
backlog_wait_time 60000
backlog_wait_time_actual 0
loginuid_immutable 0 unlocked
```

### Audit Subsystem Summary

| Parameter                | Baseline Value |
| ------------------------ | -------------: |
| Audit enabled            |            `1` |
| Failure mode             |            `1` |
| Audit daemon PID         |          `687` |
| Rate limit               |            `0` |
| Backlog limit            |         `8192` |
| Lost events              |            `0` |
| Current backlog          |            `0` |
| Backlog wait time        |        `60000` |
| Actual backlog wait time |            `0` |
| Login UID immutable      |            `0` |
| Login UID state          |     `unlocked` |

The baseline recorded **zero lost audit events** and **zero events currently waiting in the backlog**.

## Active Audit Rules

The active audit rules were checked using:

```bash
sudo auditctl -l
```

Baseline result:

```text
No rules
```

Therefore, **no explicit audit rules were reported by `auditctl -l` during baseline collection**.

This is an important baseline condition and should not be replaced with assumed rules.

## Elastic SIEM Correlation

The Linux endpoint uses Elastic Agent to provide telemetry to the CatchMe SIEM environment.

```text
Linux Endpoint
soc-linux
192.168.56.20
       |
       v
     Auditd
       |
       v
  Elastic Agent
       |
       v
 catchme-siem
192.168.56.10
       |
       v
 Elastic SIEM
       |
       v
Threat Hunting
       |
       v
Detection
       |
       v
Investigation
```

## SOC Investigation Relevance

Auditd telemetry can be correlated with:

* User identity
* Process execution
* Parent process
* Command-line activity
* File activity
* Authentication events
* SSH activity
* Sudo activity
* Persistence activity
* Network activity
* Elastic Agent telemetry
* Elastic SIEM alerts

Example investigation flow:

```text
Audit Event
     |
     v
User Identity
     |
     v
Process / Command
     |
     v
File or Configuration Change
     |
     v
Privilege Activity
     |
     v
Network Activity
     |
     v
Elastic SIEM
     |
     v
Threat Hunting
     |
     v
Incident Investigation
```
