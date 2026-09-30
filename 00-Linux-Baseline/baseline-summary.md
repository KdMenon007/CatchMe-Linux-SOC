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

# System Information Baseline

## Endpoint Identity

| Field            | Value                        |
| ---------------- | ---------------------------- |
| Hostname         | `soc-linux`                  |
| Operating System | Ubuntu 24.04.4 LTS           |
| Distribution     | Ubuntu                       |
| Version          | `24.04.4 LTS (Noble Numbat)` |
| Kernel           | `Linux 6.8.0-142-generic`    |
| Architecture     | `aarch64 (arm64)`            |
| Virtualization   | VMware                       |
| Hardware Vendor  | VMware, Inc.                 |
| Hardware Model   | VMware20,1                   |
| Chassis          | Virtual Machine              |

## Firmware

| Field            | Value                                 |
| ---------------- | ------------------------------------- |
| Firmware Version | `VMW201.00V.25275966.BA64.2603102050` |
| Firmware Date    | `2026-03-10`                          |

## CPU

| Field            | Value                   |
| ---------------- | ----------------------- |
| CPU Architecture | `aarch64`               |
| vCPU Count       | `4`                     |
| Online CPU List  | `0-3`                   |
| CPU Model        | Not reported by `lscpu` |

## Memory

| Resource |   Total |    Used | Available |
| -------- | ------: | ------: | --------: |
| RAM      | 3.8 GiB | 667 MiB |   3.2 GiB |
| Swap     | 3.8 GiB | 256 KiB |   3.8 GiB |

## Storage

| Filesystem                  | Type     |    Size |    Used | Available | Usage |
| --------------------------- | -------- | ------: | ------: | --------: | ----: |
| `/`                         | ext4     |  33 GiB | 9.8 GiB |    22 GiB |   32% |
| `/boot`                     | ext4     | 2.0 GiB | 198 MiB |   1.6 GiB |   11% |
| `/boot/efi`                 | vfat     | 1.1 GiB | 6.4 MiB |   1.1 GiB |    1% |
| `/run`                      | tmpfs    | 392 MiB | 1.3 MiB |   390 MiB |    1% |
| `/dev/shm`                  | tmpfs    | 2.0 GiB |       0 |   2.0 GiB |    0% |
| `/run/lock`                 | tmpfs    | 5.0 MiB |       0 |   5.0 MiB |    0% |
| `/run/user/1000`            | tmpfs    | 392 MiB |  12 KiB |   392 MiB |    1% |
| `/sys/firmware/efi/efivars` | efivarfs | 256 KiB |  34 KiB |   223 KiB |   14% |

## System Runtime

| Field                         | Value                |
| ----------------------------- | -------------------- |
| Uptime at Baseline Collection | `3 hours 43 minutes` |
| Logged-in Users               | `3`                  |
| Load Average                  | `0.01, 0.01, 0.00`   |

## Kernel Information

```text
Linux soc-linux 6.8.0-142-generic
#142-Ubuntu SMP PREEMPT_DYNAMIC
Wed Sep 2 14:30:29 UTC 2026
aarch64 aarch64 aarch64 GNU/Linux
```

## Security Monitoring Context

The endpoint is the Linux monitoring system in the CatchMe Linux SOC laboratory.

```text
soc-linux
Ubuntu 24.04.4 LTS
        |
        +---- Elastic Agent
        |
        +---- Auditd
        |
        +---- Linux System Logs
        |
        v
elastic-siem
192.168.1.11
```

## Baseline Security Relevance

This system information establishes the known-good operating environment before controlled attack simulations.

Future investigations can compare observed activity against this baseline, including:

* Unexpected operating system changes
* Kernel changes
* Unexpected system architecture changes
* Resource consumption anomalies
* Disk utilization changes
* Unexpected processes
* Unexpected services
* System modifications
* Abnormal system load

## Baseline Collection

The information documented above was collected from the `soc-linux` endpoint during the initial CatchMe Linux SOC baseline process.

Sensitive identifiers such as the Machine ID and Boot ID are intentionally excluded from the public portfolio documentation.

```
```
