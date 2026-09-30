# CatchMe Linux SOC — Linux Baseline

## Overview

This baseline establishes the known-good state of the Linux endpoint used in the CatchMe Linux SOC laboratory.

The purpose is to document the normal operating state of the endpoint before conducting controlled security attack simulations.

A reliable baseline allows security events observed during later investigations to be compared against the known-good system state.

---

## Objectives

The baseline documents:

- Operating system and kernel information
- Network configuration
- Listening ports and active connections
- Running and enabled services
- Local users and groups
- Privilege and sudo configuration
- SSH configuration
- Scheduled tasks and persistence mechanisms
- Auditd configuration
- Elastic Agent status
- Security-relevant system information

---

## Lab Architecture

                    ELASTIC SIEM
                    192.168.1.11
                         |
                         |
                    Fleet Server
                         |
                         |
                    Elastic Agent
                         |
                         |
                         v
                 LINUX ENDPOINT
                    soc-linux
                  192.168.1.16
                         |
          +--------------+--------------+
          |              |              |
       System          Auditd        SSH/Auth
       Logs            Events         Logs




## SOC Investigation Lifecycle
```text
Known-Good Baseline
        |
        v
Controlled Attack
        |
        v
Linux Telemetry
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
Alert Investigation
        |
        v
MITRE ATT&CK Mapping
        |
        v
Incident Report
        |
        v
Remediation
        |
        v
Re-baseline
```
---

## Lab Environment

| Component      | Hostname     | IP Address   | Role                         |
| -------------- | ------------ | ------------ | ---------------------------- |
| Elastic SIEM   | elastic-siem | 192.168.1.11 | SIEM / Kibana / Fleet Server |
| Linux Endpoint | soc-linux    | 192.168.1.16 | Security monitoring endpoint |

---

## Baseline Categories

### System Information

Documents:

* Operating system
* Kernel
* CPU
* Memory
* Disk usage
* System uptime

File:

`system-information.md`

### Network Baseline

Documents:

* Network interfaces
* IP configuration
* Routing
* DNS
* Listening ports
* Active connections

File:

`network-baseline.md`

### Services Baseline

Documents:

* Running services
* Enabled services
* Failed services
* Systemd services

File:

`services-baseline.md`

### Users and Privileges

Documents:

* Local users
* Groups
* UID/GID information
* Sudo configuration
* Privileged accounts
* UID 0 accounts

File:

`users-and-privileges.md`

### SSH Baseline

Documents:

* SSH service
* SSH listening configuration
* Authentication configuration
* Root login configuration
* Password authentication
* Public-key authentication
* Authentication activity

File:

`ssh-baseline.md`

### Persistence Baseline

Documents:

* User cron jobs
* System cron directories
* Systemd timers
* Enabled systemd units

File:

`persistence-baseline.md`

### Auditd Baseline

Documents:

* Audit subsystem status
* Active audit rules
* Linux auditing configuration

File:

`auditd-baseline.md`

### Elastic Agent Baseline

Documents:

* Elastic Agent status
* Agent version
* Fleet connectivity
* Elastic SIEM resolution
* Fleet communication

File:

`elastic-agent-baseline.md`

---

## Why Baseline Before Attack Simulation?

Security investigations require context.

Without a known-good baseline, it can be difficult to determine whether an observed process, service, network connection, scheduled task, or configuration change is normal or suspicious.

The CatchMe Linux SOC therefore establishes a baseline before beginning controlled attack simulations.

Later projects will compare observed activity against this baseline.

---

## Baseline vs Investigation

| Normal Baseline           | Later Investigation          |
| ------------------------- | ---------------------------- |
| Normal processes          | Suspicious process execution |
| Normal services           | Unexpected service           |
| Normal network ports      | New listening port           |
| Normal users              | New or abused account        |
| Normal SSH configuration  | SSH persistence              |
| Normal cron/systemd state | Persistence mechanism        |
| Normal audit activity     | Suspicious system activity   |
| Normal Elastic telemetry  | Attack-generated telemetry   |

---

## Evidence

Baseline evidence will be stored in:

```text
evidence/
```

Screenshots and relevant sanitized evidence will be added as the portfolio develops.

---

## Security and Sanitization

This repository is a public cybersecurity portfolio.

Never commit:

* Passwords
* API keys
* Enrollment tokens
* Service tokens
* Private keys
* Authentication secrets
* `/etc/shadow`
* Sensitive credentials
* Real customer information
* Confidential logs

All evidence must be reviewed and sanitized before publication.

---

## Project Status

| Phase                     | Status      |
| ------------------------- | ----------- |
| Lab Setup                 | Completed   |
| Linux Baseline Collection | Completed   |
| Baseline Documentation    | In Progress |
| GitHub Portfolio          | In Progress |
| Project 01                | Not Started |
| Total Projects            | 25          |

---

## Next Phase

After the Linux baseline documentation is completed, the CatchMe Linux SOC project series will begin.

### Project 01

**SSH Brute Force Investigation**

The investigation workflow will follow:

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
MITRE ATT&CK
  ↓
Incident Report
  ↓
Remediation
```
