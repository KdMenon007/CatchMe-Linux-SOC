````markdown
# Linux Baseline Summary

## Purpose

This document provides a concise security baseline for the `soc-linux` Linux endpoint before controlled attack simulations begin.

The baseline represents the known-good state used for comparison during future SOC investigations.

## Environment

| Item | Value |
|---|---|
| Endpoint Hostname | `soc-linux` |
| Endpoint IP | `192.168.1.16` |
| SIEM Hostname | `elastic-siem` |
| SIEM IP | `192.168.1.11` |
| SIEM Platform | Elastic Security |
| Endpoint Agent | Elastic Agent |
| Linux Auditing | Auditd |

## Baseline Scope

The baseline covers:

- System information
- Network configuration
- Listening services
- Running services
- Enabled services
- Local users and groups
- Privilege and sudo configuration
- SSH configuration
- Cron and scheduled tasks
- Systemd persistence
- Auditd
- Elastic Agent
- Running processes
- Active network connections

## Baseline Workflow

```text
Known-Good Baseline
        ↓
Controlled Attack
        ↓
Linux Telemetry
        ↓
Threat Hunting
        ↓
Detection
        ↓
Investigation
        ↓
MITRE ATT&CK
        ↓
Incident Response
        ↓
Remediation
        ↓
Re-baseline
````

## Baseline Evidence

The baseline collection includes:

* Host information
* Operating system information
* Kernel information
* CPU and memory information
* Disk usage
* Network configuration
* Routing
* DNS
* Listening ports
* Active connections
* Running services
* Enabled services
* Failed services
* User accounts
* Groups
* Sudo configuration
* UID 0 accounts
* SSH configuration
* Cron configuration
* Systemd timers
* Auditd status and rules
* Elastic Agent status
* Elastic Agent version
* Fleet connectivity
* Process information

## Security Baseline Concept

A baseline provides context for security monitoring.

Examples:

```text
Known Service
     ↓
Unexpected Service
     ↓
Investigation
```

```text
Known Listening Port
     ↓
New Listening Port
     ↓
Investigation
```

```text
Known User
     ↓
Unexpected Account Activity
     ↓
Investigation
```

```text
Known Persistence State
     ↓
New Cron/Systemd Persistence
     ↓
Investigation
```

## Baseline Status

| Item                   | Status      |
| ---------------------- | ----------- |
| Baseline Collection    | Completed   |
| Baseline Documentation | In Progress |
| Attack Simulations     | Not Started |
| Planned Investigations | 25          |

```
```
