# Project 02 — Environment Verification

## Valid Account → SSH Hijacking Investigation

## Purpose

This document verifies that the CatchMe Linux SOC laboratory is operational before starting the controlled Project 02 attack simulation.

The verification covers:

- Kali attacker
- Linux endpoint
- Elastic SIEM
- Elasticsearch
- Kibana
- Fleet Server
- Elastic Agent
- SSH service
- Audit subsystem
- Network connectivity
- Fleet API connectivity

---

# 1. Lab Architecture

```text
┌──────────────────────┐
│     Kali Linux       │
│      Attacker        │
│    192.168.1.10      │
└──────────┬───────────┘
           │
           │ SSH / TCP 22
           ▼
┌──────────────────────┐
│      soc-linux       │
│    Linux Endpoint    │
│    192.168.1.16      │
│                      │
│   SSH + Auditd       │
│   Elastic Agent      │
└──────────┬───────────┘
           │
           │ Telemetry
           ▼
┌──────────────────────┐
│     elastic-siem     │
│     Elastic SIEM     │
│    192.168.1.11      │
│                      │
│ Elasticsearch        │
│ Kibana               │
│ Fleet Server         │
│ Elastic Agent        │
└──────────────────────┘
```

---

# 2. Kali Attacker Verification

## Hostname

```text
kiran
```

## Primary Lab IP

```text
192.168.1.10/24
```

## Collection Time

```text
02 October 2026
07:39:10 IST
```

## Role

```text
Attack Simulation
```

The `eth0` interface is used as the fixed Project 02 attack source.

The `wlan0` interface also has address `192.168.1.9`, but the controlled lab attack source remains:

```text
192.168.1.10
```

---

# 3. Linux Endpoint Verification

## Hostname

```text
soc-linux
```

## IP Address

```text
192.168.1.16/24
```

## System Time

```text
02 October 2026
02:09:26 UTC
```

## Role

```text
Monitored Linux Endpoint
```

---

# 4. Elastic Agent Verification

Command:

```bash
sudo elastic-agent status
```

Observed result:

```text
fleet
└─ status: (HEALTHY) Connected

elastic-agent
└─ status: (HEALTHY) Running
```

Result:

```text
HEALTHY
```

The endpoint is successfully connected to Fleet Server and the Elastic Agent is running.

---

# 5. SSH Service Verification

Command:

```bash
sudo systemctl status ssh --no-pager
```

Observed state:

```text
Active: active (running)
```

SSH listeners:

```text
0.0.0.0:22
:::22
```

Result:

```text
SSH service operational.
```

---

# 6. Audit Subsystem Verification

Command:

```bash
sudo auditctl -s
```

Observed:

```text
enabled 1
failure 1
pid 711
rate_limit 0
backlog_limit 8192
lost 0
backlog 0
backlog_wait_time 60000
backlog_wait_time_actual 0
loginuid_immutable 0
unlocked
```

Important state:

```text
enabled = 1
lost = 0
```

Result:

```text
Audit subsystem enabled with no lost events reported during verification.
```

---

# 7. Elastic SIEM Verification

## Hostname

```text
elastic-siem
```

## IP Address

```text
192.168.1.11/24
```

## System Time

```text
02 October 2026
02:09:37 UTC
```

## Role

```text
Elastic SIEM
```

---

# 8. Elasticsearch Verification

Command:

```bash
sudo systemctl status elasticsearch --no-pager
```

Observed:

```text
Active: active (running)
```

Result:

```text
Elasticsearch operational.
```

---

# 9. Kibana Verification

Command:

```bash
sudo systemctl status kibana --no-pager
```

Observed:

```text
Active: active (running)
```

Result:

```text
Kibana operational.
```

---

# 10. Fleet Server Verification

Command:

```bash
sudo systemctl status elastic-agent --no-pager
```

Observed:

```text
Active: active (running)
```

Fleet API:

```text
https://192.168.1.11:8220/api/status
```

Observed response:

```json
{
  "name": "fleet-server",
  "status": "HEALTHY"
}
```

Result:

```text
Fleet Server HEALTHY.
```

---

# 11. Network Verification

## Linux Endpoint → Elastic SIEM

The endpoint successfully communicated with:

```text
192.168.1.11
```

Fleet Server TCP port:

```text
8220
```

The endpoint successfully established TCP connectivity to the Fleet Server.

---

# 12. Kali → Linux SSH Verification

The attacker system successfully reached:

```text
192.168.1.16:22
```

Verification:

```text
Connection to 192.168.1.16 22 port [tcp/ssh] succeeded!
```

Result:

```text
SSH network path operational.
```

---

# 13. SSH Authentication Configuration

The Linux endpoint was verified with:

```bash
sudo sshd -T | grep -E "passwordauthentication|permitrootlogin|pubkeyauthentication"
```

Observed:

```text
permitrootlogin without-password
pubkeyauthentication yes
passwordauthentication yes
```

Therefore:

* Password authentication is enabled.
* Public-key authentication is enabled.
* Root password SSH authentication is disabled.

---

# 14. Authorized Key Verification

The target account is:

```text
socadmin
```

SSH directory:

```text
/home/socadmin/.ssh
```

The authorized key file exists:

```text
/home/socadmin/.ssh/authorized_keys
```

Current size:

```text
0 bytes
```

Result:

```text
No authorized SSH public keys are configured for socadmin at baseline.
```

---

# 15. Pre-Existing SSH Activity

During environment verification, the Linux SSH journal contained:

```text
Accepted password for socadmin from 192.168.1.10
```

Timestamp:

```text
02 October 2026
01:56:42 UTC
```

This activity occurred during environment validation before the controlled Project 02 attack.

Classification:

```text
PRE-ATTACK VALIDATION ACTIVITY
```

This event will **not** be treated as Project 02 attack evidence.

It will be excluded from:

* Attack timeline
* Detection validation
* Incident findings
* Final attack evidence
* Incident report conclusions

---

# 16. Timezone Correlation

The lab uses different timezone configurations.

| System       | Timezone |
| ------------ | -------- |
| Kali         | IST      |
| soc-linux    | UTC      |
| elastic-siem | UTC      |

For Project 02 investigation timelines:

```text
IST = UTC + 05:30
```

All timestamps will be normalized when correlating attack activity across systems.

---

# 17. Environment Verification Summary

| Component       | Expected          | Observed     | Status |
| --------------- | ----------------- | ------------ | ------ |
| Kali            | 192.168.1.10      | 192.168.1.10 | PASS   |
| Linux Endpoint  | 192.168.1.16      | 192.168.1.16 | PASS   |
| Elastic SIEM    | 192.168.1.11      | 192.168.1.11 | PASS   |
| Elasticsearch   | Running           | Running      | PASS   |
| Kibana          | Running           | Running      | PASS   |
| Fleet Server    | Healthy           | Healthy      | PASS   |
| Elastic Agent   | Healthy           | Healthy      | PASS   |
| SSH             | Running           | Running      | PASS   |
| Auditd          | Enabled           | Enabled      | PASS   |
| Kali → SSH      | Reachable         | Reachable    | PASS   |
| Authorized Keys | Baseline reviewed | Empty        | PASS   |

---

# 18. Environment Verification Conclusion

The CatchMe Linux SOC laboratory is operational and ready for the controlled Project 02 simulation.

The following critical components are confirmed:

```text
Kali Attacker
      ↓
SSH Connectivity
      ↓
Linux Endpoint
      ↓
Elastic Agent
      ↓
Fleet Server
      ↓
Elastic SIEM
      ↓
SOC Investigation
```

The environment baseline has been captured before the controlled attack.



```
```
