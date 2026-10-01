# Project 01 — Environment Verification

## Purpose

This document verifies that the CatchMe Linux SOC lab is operational immediately before the Project 01 SSH brute-force simulation.

The verification confirms:

- Fixed lab IP addressing
- Linux endpoint identity
- SSH service availability
- Elastic Agent health
- Fleet connectivity
- SIEM connectivity
- Auditd status
- Attacker-to-target connectivity
- Required telemetry path

---

## 1. Fixed Lab Topology

| Role | Hostname | IP Address | Function |
|---|---|---|---|
| 🟥 Attacker | `kiran` | `192.168.1.10` | Kali Linux / attack simulation |
| 🟩 Linux Endpoint | `soc-linux` | `192.168.1.16` | Ubuntu endpoint / telemetry source |
| 🟦 Elastic SIEM | `elastic-siem` | `192.168.1.11` | Elasticsearch / Kibana / Fleet Server |
| Gateway | — | `192.168.1.1` | Network gateway |
| Network | — | `192.168.1.0/24` | CatchMe SOC lab network |

### Fixed Addressing Rule

Project 01 uses:

```text
Kali attacker      → 192.168.1.10
Linux endpoint     → 192.168.1.16
Elastic SIEM       → 192.168.1.11
```
These addresses are treated as fixed project infrastructure and must not be changed in project documentation.

---

# 2. Linux Endpoint Verification

## Target

```text
Hostname: soc-linux
IP:       192.168.1.16
OS:       Ubuntu 24.04.4 LTS
Kernel:   6.8.0-142-generic
Architecture: ARM64 / aarch64
```

The Linux endpoint is the system being monitored during Project 01.

---

# 3. Network Verification

The Linux endpoint must have:

```text
192.168.1.16/24
```

with the lab gateway:

```text
192.168.1.1
```

and connectivity to:

```text
Elastic SIEM: 192.168.1.11
Kali:         192.168.1.10
```

Expected route:

```text
192.168.1.0/24
```

---

# 4. SSH Service Verification

Project 01 targets the SSH service.

Expected configuration:

| Setting                | Baseline           |
| ---------------------- | ------------------ |
| Service                | `ssh.service`      |
| Protocol               | SSH                |
| Port                   | TCP/22             |
| IPv4 listener          | `0.0.0.0:22`       |
| IPv6 listener          | `[::]:22`          |
| MaxAuthTries           | `6`                |
| PasswordAuthentication | `yes`              |
| PubkeyAuthentication   | `yes`              |
| PermitEmptyPasswords   | `no`               |
| PermitRootLogin        | `without-password` |

The SSH service must be running before the attack simulation.

---

# 5. SSH Connectivity Verification

The attacker must be able to reach the target SSH service:

```text
Kali
192.168.1.10
     │
     │ TCP/22
     ▼
soc-linux
192.168.1.16
```

The connectivity test completed successfully before Project 01.

This confirms that the attack path is available without requiring any change to the network configuration.

---

# 6. Elastic Agent Verification

The Linux endpoint uses Elastic Agent for telemetry collection.

Expected state:

```text
fleet
└─ status: (HEALTHY) Connected

elastic-agent
└─ status: (HEALTHY) Running
```

Agent version:

```text
9.5.2
```

Fleet Server:

```text
https://elastic-siem:8220
```

Elastic SIEM:

```text
192.168.1.11
```

Policy:

```text
SOC-Linux-Policy
```

---

# 7. Fleet Connectivity

The Linux endpoint maintains an established connection to Fleet Server:

```text
soc-linux
192.168.1.16
      │
      │ TCP/8220
      ▼
elastic-siem
192.168.1.11
```

The Fleet Server API returned:

```text
HTTP 200
```

with:

```json
{
  "name": "fleet-server",
  "status": "HEALTHY"
}
```

This confirms that the endpoint can communicate with Fleet Server before attack execution.

---

# 8. Auditd Verification

Auditd is enabled on the Linux endpoint.

Baseline state:

| Parameter          |  Value |
| ------------------ | -----: |
| Enabled            |    `1` |
| Failure mode       |    `1` |
| Rate limit         |    `0` |
| Backlog limit      | `8192` |
| Lost events        |    `0` |
| Backlog            |    `0` |
| Custom audit rules |   None |

Auditd provides additional Linux security telemetry for later projects and investigations.

For Project 01, SSH authentication telemetry is primarily observed through the SSH/system logging pipeline.

---

# 9. User and Privilege Verification

Primary lab account:

```text
socadmin
```

UID:

```text
1000
```

The account has administrative privileges through the `sudo` group.

The only UID 0 account identified during the baseline was:

```text
root
```

No Project 01 persistence or privilege-escalation activity was performed before the attack.

---

# 10. Persistence Verification

Before Project 01:

```text
socadmin crontab:
No crontab
```

Normal systemd timers were present.

No Project 01 persistence mechanism was established before the SSH attack.

This establishes a clean baseline for later comparison.

---

# 11. Telemetry Path Verification

The expected telemetry pipeline is:

```text
Kali
192.168.1.10
      │
      │ SSH authentication traffic
      ▼
soc-linux
192.168.1.16
      │
      │ SSH / system telemetry
      ▼
Elastic Agent
      │
      ▼
Fleet Server
192.168.1.11
      │
      ▼
Elastic SIEM
      │
      ▼
Kibana Discover
      │
      ▼
KQL Threat Hunting
      │
      ▼
Detection Rule
      │
      ▼
Alert Investigation
```

---

# 12. Pre-Attack Authentication State

Before the controlled Hydra simulation, the endpoint contained legitimate administrative SSH activity from:

```text
192.168.1.10
```

This activity was associated with normal lab administration.

These existing sessions must not be incorrectly attributed to the Project 01 Hydra attack.

The investigation therefore uses the controlled attack time window to separate attack-generated events from earlier legitimate activity.

---

# 13. Time Synchronization

The two systems use different display time zones:

| Host           | Time Zone          |
| -------------- | ------------------ |
| `soc-linux`    | UTC                |
| `kiran` / Kali | Asia/Kolkata (IST) |

Both systems were synchronized using NTP.

For Project 01 investigation:

```text
UTC + 05:30 = IST
```

Example:

```text
03:27:17 UTC
=
08:57:17 IST
```

Timezone normalization is required when correlating Kali attack timestamps with Linux SSH logs and Elastic/Kibana events.

---

# 14. SIEM Readiness

The following conditions were confirmed before attack execution:

| Component            | Status              |
| -------------------- | ------------------- |
| Linux endpoint       | Ready               |
| SSH service          | Listening on TCP/22 |
| Kali → Linux TCP/22  | Reachable           |
| Elastic Agent        | Healthy             |
| Fleet connection     | Healthy             |
| Fleet Server         | Healthy             |
| Auditd               | Enabled             |
| SIEM connectivity    | Confirmed           |
| Network addressing   | Fixed               |
| Time synchronization | Active              |

---

# 15. Project 01 Investigation Window

The successful detection-validation attack was isolated to:

```text
08:55–09:00 IST
```

This window was selected to exclude the earlier test performed around:

```text
08:29 IST
```

The isolated window contained the four authentication-failure events used to validate the detection.

---

# 16. Environment Verification Conclusion

The CatchMe Linux SOC environment was operational before Project 01 execution.

The verified attack path was:

```text
Kali
192.168.1.10
        │
        │ SSH / TCP 22
        ▼
soc-linux
192.168.1.16
        │
        │ Elastic Agent
        ▼
elastic-siem
192.168.1.11
        │
        ▼
Elastic SIEM
```

The environment was therefore ready for controlled SSH brute-force simulation and subsequent telemetry, threat-hunting, detection, and investigation activities.

---

## Evidence Checklist

* [x] Fixed IP addressing verified
* [x] Linux hostname verified
* [x] SSH service verified
* [x] TCP/22 connectivity verified
* [x] Elastic Agent verified
* [x] Fleet connectivity verified
* [x] Fleet Server health verified
* [x] Auditd verified
* [x] User/privilege baseline verified
* [x] Persistence baseline verified
* [x] Time synchronization verified
* [x] Telemetry path verified
* [x] Investigation window defined
* [x] Pre-existing legitimate SSH activity distinguished from attack activity

---

