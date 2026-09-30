# 🐧 CatchMe Linux SOC — Linux Baseline

## Overview

This baseline establishes the known-good state of the Linux endpoint used in the CatchMe Linux SOC laboratory.

The purpose is to document the normal operating state of the endpoint before conducting controlled security attack simulations.

A reliable baseline allows security events observed during later investigations to be compared against the known-good system state.

---

## 🎯 Objectives

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

## 🏗️ Lab Architecture

```text
                 ┌─────────────────────────┐
                 │     Elastic SIEM        │
                 │     192.168.1.11        │
                 │                         │
                 │ Elasticsearch           │
                 │ Kibana                  │
                 │ Fleet Server            │
                 └────────────┬────────────┘
                              │
                              │ Elastic Agent
                              │
                              ▼
                 ┌─────────────────────────┐
                 │      Linux Endpoint     │
                 │      soc-linux          │
                 │      192.168.1.16       │
                 │                         │
                 │ System Logs             │
                 │ Auditd                  │
                 │ Authentication Logs     │
                 │ Process / System Data   │
                 └─────────────────────────┘

🔄 SOC Investigation Lifecycle

The baseline represents the starting point of the CatchMe Linux SOC investigation lifecycle.

Known-Good State
       │
       ▼
Controlled Attack
       │
       ▼
Linux Telemetry
       │
       ▼
Elastic SIEM
       │
       ▼
Threat Hunting
       │
       ▼
Detection
       │
       ▼
Alert Investigation
       │
       ▼
MITRE ATT&CK Mapping
       │
       ▼
Incident Report
       │
       ▼
Remediation
       │
       ▼
Re-baseline


## 🖥️ Endpoint

| Component       | Value                              |
| --------------- | ---------------------------------- |
| Hostname        | `soc-linux`                        |
| Role            | Linux Security Monitoring Endpoint |
| IP Address      | `192.168.1.16`                     |
| SIEM            | Elastic Security                   |
| Agent           | Elastic Agent                      |
| Audit Framework | Auditd                             |

---

## 🛡️ SIEM Infrastructure

| Component                 | Value            |
| ------------------------- | ---------------- |
| Hostname                  | `elastic-siem`   |
| IP Address                | `192.168.1.11`   |
| Platform                  | Elastic Security |
| Fleet Server              | Enabled          |
| Endpoint Agent Management | Fleet            |

---

## 📊 Baseline Categories

### 1. System Information

Documents:

* Hostname
* Operating system
* Kernel
* CPU
* Memory
* Disk usage
* System uptime

See:

[`system-information.md`](system-information.md)

---

### 2. Network Baseline

Documents:

* Network interfaces
* Assigned IP addresses
* Routing configuration
* DNS configuration
* Listening TCP/UDP ports
* Active network connections

See:

[`network-baseline.md`](network-baseline.md)

---

### 3. Services Baseline

Documents:

* Running services
* Enabled services
* Failed services
* Systemd services

See:

[`services-baseline.md`](services-baseline.md)

---

### 4. Users & Privileges

Documents:

* Local users
* Groups
* UID/GID information
* Sudo configuration
* Privileged accounts
* UID 0 accounts

See:

[`users-and-privileges.md`](users-and-privileges.md)

---

### 5. SSH Baseline

Documents:

* SSH service state
* SSH listening configuration
* Authentication configuration
* Root login configuration
* Password authentication
* Public-key authentication
* Authentication activity

See:

[`ssh-baseline.md`](ssh-baseline.md)

---

### 6. Persistence Baseline

Documents:

* User cron jobs
* System cron directories
* Systemd timers
* Enabled systemd units

See:

[`persistence-baseline.md`](persistence-baseline.md)

---

### 7. Auditd Baseline

Documents:

* Audit subsystem status
* Active audit rules
* Linux auditing configuration

See:

[`auditd-baseline.md`](auditd-baseline.md)

---

### 8. Elastic Agent Baseline

Documents:

* Elastic Agent status
* Agent version
* Fleet connectivity
* Elastic SIEM resolution
* Fleet communication

See:

[`elastic-agent-baseline.md`](elastic-agent-baseline.md)

---

## 🔬 Why Baseline Before Attack Simulation?

Security investigations require context.

Without a known-good baseline, it can be difficult to determine whether an observed process, service, network connection, scheduled task, or configuration change is normal or suspicious.

The CatchMe Linux SOC therefore establishes a baseline before beginning controlled attack simulations.

Later projects will compare observed activity against this baseline.

---

## 🧪 Baseline → Attack Comparison

| Baseline                  | Investigation                |
| ------------------------- | ---------------------------- |
| Normal processes          | Suspicious process execution |
| Normal services           | Unexpected service           |
| Normal network ports      | New listening port           |
| Normal users              | New/abused account           |
| Normal SSH configuration  | SSH persistence              |
| Normal cron/systemd state | Persistence mechanism        |
| Normal audit activity     | Suspicious system activity   |
| Normal Elastic telemetry  | Attack-generated telemetry   |

---

## 📁 Evidence

Evidence associated with the baseline is stored under:

```text
evidence/
```

Screenshots and other evidence will be added as the portfolio is developed.

---

## 🔐 Security & Sanitization

This repository is a public cybersecurity portfolio.

The following information must **never** be committed:

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

Evidence uploaded to GitHub must be reviewed and sanitized before publication.

---

## 📌 Status

**Baseline Collection:** Completed

**Portfolio Documentation:** In Progress

**Attack Simulations:** Not Started

**Projects Planned:** 25

---

## 🔗 Next Phase

After the Linux baseline is documented, the CatchMe Linux SOC project series will begin with:

**Project 01 — SSH Brute Force Investigation**

The investigation will follow:

```text
Attack
  ↓
Telemetry
  ↓
Threat Hunt
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
