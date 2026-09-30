# Elastic Agent Baseline

## Purpose

This document records the verified Elastic Agent health, version, Fleet connectivity, hostname resolution, and service state on the CatchMe Linux SOC endpoint before attack simulations begin.

## Lab Environment

| Component | Value |
|---|---|
| Linux Endpoint | `soc-linux` |
| Linux Endpoint IP | `192.168.1.16` |
| Elastic SIEM | `elastic-siem` |
| Elastic SIEM IP | `192.168.1.11` |
| Fleet Server | `elastic-siem` |
| Fleet Server Port | `8220` |
| Fleet Server URL | `https://elastic-siem:8220` |
| Fleet Policy | `SOC-Linux-Policy` |
| Policy Revision | `4` |

## Elastic Agent Health

Verified with:

```bash
sudo elastic-agent status
```

Result:

```text
fleet
└─ status: (HEALTHY) Connected
elastic-agent
└─ status: (HEALTHY) Running
```

| Component        | Status                |
| ---------------- | --------------------- |
| Fleet connection | `HEALTHY / Connected` |
| Elastic Agent    | `HEALTHY / Running`   |

## Elastic Agent Version

Verified version:

```text
Binary: 9.5.2
Daemon: 9.5.2
Build: 92fc15c077f79efc81f2b1bfa72d2e250d3ed6c6
Build Date: 2026-08-17 19:31:55 UTC
```

| Component | Version |
| --------- | ------- |
| Binary    | `9.5.2` |
| Daemon    | `9.5.2` |

The Binary and Daemon versions match.

## Fleet Server Resolution

Verified with:

```bash
getent hosts elastic-siem
```

Result:

```text
192.168.1.11    elastic-siem
```

Expected hostname resolution:

```text
elastic-siem → 192.168.1.11
```

## Fleet Server Connection

Verified with:

```bash
sudo ss -ntp | grep ':8220'
```

Result:

```text
ESTAB 0 0 192.168.1.16:35562 192.168.1.11:8220 users:(("elastic-agent",pid=4444,fd=14))
```

This confirms an established TCP connection from the Linux endpoint to Fleet Server on port `8220`.

| Source               | Destination         | Status     |
| -------------------- | ------------------- | ---------- |
| `192.168.1.16:35562` | `192.168.1.11:8220` | `ESTAB`    |
| Process              | `elastic-agent`     | PID `4444` |

## Elastic Agent Systemd Service

Verified with:

```bash
systemctl status elastic-agent --no-pager
```

Service:

```text
elastic-agent.service - Elastic Agent is a unified agent to observe, monitor and protect your system.
```

Service state:

```text
Loaded: loaded (/etc/systemd/system/elastic-agent.service; enabled; preset: enabled)
Active: active (running)
Main PID: 4444 (elastic-agent)
Tasks: 22
Memory: 202.1M
Memory Peak: 263.1M
CPU: 1min 14.712s
```

The service has been running since:

```text
2026-09-30 07:56:51 UTC
```

## Elastic Agent Process Tree

Observed baseline:

```text
elastic-agent.service
└── elastic-agent
    └── elastic-ot…
```

Main Elastic Agent PID:

```text
4444
```

## Fleet and Telemetry Architecture

```text
┌─────────────────────────────┐
│       Linux Endpoint        │
│         soc-linux           │
│       192.168.1.16          │
└──────────────┬──────────────┘
               │
               │ Elastic Agent
               │
               │ HTTPS :8220
               ▼
┌─────────────────────────────┐
│       Fleet Server          │
│        elastic-siem         │
│       192.168.1.11          │
│          :8220              │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│        Elastic SIEM         │
│ Elasticsearch + Kibana      │
└─────────────────────────────┘
```

## Baseline Health Summary

| Category            | Verified State   |
| ------------------- | ---------------- |
| Elastic Agent       | Healthy          |
| Fleet connection    | Connected        |
| Agent service       | Active / Running |
| Agent startup       | Enabled          |
| Agent Binary        | `9.5.2`          |
| Agent Daemon        | `9.5.2`          |
| Hostname resolution | Correct          |
| Fleet Server IP     | `192.168.1.11`   |
| Fleet Server port   | `8220`           |
| TCP connection      | Established      |
| Agent PID           | `4444`           |

## SOC Investigation Relevance

This baseline establishes the expected Elastic Agent state before the CatchMe Linux attack simulations.

Future investigations can compare against this baseline for:

* Elastic Agent service stoppage
* Fleet disconnection
* Unexpected destination changes
* Unexpected Agent version changes
* Agent process termination
* Agent process replacement
* Unexpected resource consumption
* Telemetry interruption
* Fleet policy changes
* Auditd telemetry interruption

Example investigation flow:

```text
Linux Activity
      |
      v
Elastic Agent
      |
      +---- System Telemetry
      |
      +---- Auditd Telemetry
      |
      v
Fleet Server
      |
      v
Elastic SIEM
      |
      v
KQL Threat Hunting
      |
      v
Detection
      |
      v
Investigation
```


