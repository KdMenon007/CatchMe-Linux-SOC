
```text
CatchMe-Linux-SOC/00-Linux-Baseline/
```

### Filename

```text
baseline-summary.md
```

### Copy everything below

````markdown
# Linux Baseline Summary

## Purpose

This document provides a concise security baseline for the `soc-linux` Linux endpoint before controlled attack simulations begin.

The baseline represents the known-good state used for comparison during future SOC investigations.

---

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

---

## Baseline Scope

The baseline covers the following security-relevant areas:

1. System information
2. Network configuration
3. Listening services
4. Running services
5. Enabled services
6. Local users and groups
7. Privilege and sudo configuration
8. SSH configuration
9. Cron and scheduled tasks
10. Systemd persistence
11. Auditd
12. Elastic Agent
13. Running processes
14. Active network connections

---

## Security Baseline Concept

The endpoint is considered to be in a known-good state at the time of baseline collection.

Future attack simulations will introduce controlled changes to this state.

These changes will then be investigated through:

```text
Baseline
   ↓
Attack Activity
   ↓
Telemetry
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
````

---

## Baseline Evidence

The original baseline collection was performed directly on the Linux endpoint.

Collected information includes:

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

---

## Baseline Security Principle

A baseline provides context for security monitoring.

For example:

```text
Known service
     ↓
Unexpected service
     ↓
Investigate
```

```text
Known listening port
     ↓
New listening port
     ↓
Investigate
```

```text
Known user
     ↓
Unexpected account activity
     ↓
Investigate
```

```text
Known persistence state
     ↓
New cron/systemd persistence
     ↓
Investigate
```

---

## Future Comparison

The baseline will be reused throughout the CatchMe Linux SOC project series.

Each investigation will compare observed activity against the established normal state where applicable.

Examples include:

* New processes
* New services
* New network listeners
* New users
* Modified SSH configuration
* New SSH keys
* New cron jobs
* New systemd units
* Privilege changes
* Suspicious execution
* Unexpected network connections

---

## Baseline Status

**Status:** Completed

**Purpose:** Known-good Linux security state

**Next Stage:** Detailed baseline documentation

**Attack Simulations:** Not yet started

**Planned SOC Investigations:** 25

---

## Security Notice

This repository is intended as a cybersecurity portfolio and laboratory documentation project.

Sensitive information must not be committed to the repository.

Never publish:

* Passwords
* API keys
* Enrollment tokens
* Service tokens
* Private keys
* `/etc/shadow`
* Sensitive authentication data
* Confidential logs
* Real customer information

````

