# Project 02 — Baseline Check

## Valid Account → SSH Hijacking Investigation

## Purpose

This document records the security baseline of the CatchMe Linux SOC environment before performing the controlled Valid Account SSH Hijacking simulation.

The baseline ensures that:

- Existing system activity is documented
- Pre-existing SSH sessions are identified
- Authentication configuration is verified
- User and SSH settings are captured
- Later attack activity can be accurately compared against normal state

---

# 1. Baseline Collection Time

## Collection Date

```text
02 October 2026
```

## Time Reference

The lab uses multiple systems with different timezone configurations.

| System         | Timezone           |
| -------------- | ------------------ |
| Kali Linux     | Asia/Kolkata (IST) |
| Linux Endpoint | UTC                |
| Elastic SIEM   | UTC                |

All investigation timelines will normalize timestamps during analysis.

---

# 2. Linux Endpoint Baseline

## Host Information

| Parameter             | Value          |
| --------------------- | -------------- |
| Hostname              | soc-linux      |
| IP Address            | 192.168.1.16   |
| Operating System Role | Linux Endpoint |
| Monitoring Agent      | Elastic Agent  |
| SIEM Integration      | Fleet Server   |

---

# 3. Elastic Agent Status

Command:

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

Status:

```text
Telemetry collection is operational.
```

---

# 4. SSH Service Baseline

Command:

```bash
sudo systemctl status ssh --no-pager
```

Result:

```text
OpenBSD Secure Shell server

Active: active (running)
```

SSH listener:

```text
0.0.0.0:22
:::22
```

Status:

```text
SSH service available before attack simulation.
```

---

# 5. SSH Authentication Configuration

Command:

```bash
sudo sshd -T | grep -E "passwordauthentication|permitrootlogin|pubkeyauthentication"
```

Observed configuration:

```text
permitrootlogin without-password

pubkeyauthentication yes

passwordauthentication yes
```

## Configuration Analysis

### Root Login

```text
permitrootlogin without-password
```

Finding:

* Root password authentication is disabled.
* Root SSH access requires key-based authentication.

---

### Public Key Authentication

```text
pubkeyauthentication yes
```

Finding:

* SSH key authentication is enabled.
* Authorized key review is required during investigation.

---

### Password Authentication

```text
passwordauthentication yes
```

Finding:

* User password authentication is enabled.
* Valid account abuse investigation is possible.

---

# 6. SSH Authorized Keys Baseline

Command:

```bash
ls -la ~/.ssh
```

Observed:

```text
/home/socadmin/.ssh
```

Files:

```text
authorized_keys
known_hosts
known_hosts.old
```

Authorized key file:

```text
-rw------- authorized_keys
```

Size:

```text
0 bytes
```

Finding:

```text
No SSH public keys are currently authorized for socadmin.
```

---

# 7. User Authentication Baseline

Target Account:

```text
socadmin
```

Authentication method available:

```text
Password Authentication
SSH Key Authentication
```

Current authorized keys:

```text
None
```

---

# 8. Existing SSH Activity Review

Command:

```bash
sudo journalctl -u ssh
```

Observed pre-existing event:

```text
Accepted password for socadmin from 192.168.1.10
```

Timestamp:

```text
02:56:42 UTC
```

Classification:

```text
PRE-ATTACK VALIDATION ACTIVITY
```

Important:

This event is excluded from Project 02 attack evidence.

Reason:

* Generated during environment validation
* Occurred before controlled attack execution
* Not part of the attack timeline

---

# 9. Audit Framework Status

Command:

```bash
sudo auditctl -s
```

Result:

```text
enabled 1
failure 1
lost 0
backlog 0
```

Finding:

```text
Audit subsystem enabled and operational.
```

---

# 10. Network Baseline

## Linux Endpoint

```text
Interface:
enp2s0

IP:
192.168.1.16/24
```

---

## Expected Communication

```text
Linux Endpoint
      |
      |
      ↓
Elastic SIEM
192.168.1.11
```

Telemetry path:

```text
SOC-Linux
    ↓
Elastic Agent
    ↓
Fleet Server
    ↓
Elastic SIEM
```

---

# 11. Process Baseline

Before attack execution:

* No malicious processes identified
* No unauthorized SSH keys present
* No persistence mechanisms identified during baseline review

---

# 12. Baseline Security State

| Check                     | Result      |
| ------------------------- | ----------- |
| SSH Service               | Running     |
| Password Authentication   | Enabled     |
| Public Key Authentication | Enabled     |
| Root Password Login       | Disabled    |
| Authorized Keys           | Empty       |
| Elastic Agent             | Healthy     |
| Fleet Connection          | Healthy     |
| Auditd                    | Enabled     |
| Telemetry Pipeline        | Operational |

---

# 13. Baseline Conclusion

The CatchMe Linux SOC environment is ready for the controlled Valid Account SSH Hijacking simulation.

The baseline confirms:

```text
Linux Endpoint
        ↓
SSH Available
        ↓
Authentication Monitoring Enabled
        ↓
Elastic Telemetry Active
        ↓
Investigation Ready
```

Any authentication events generated after this baseline point will be evaluated against this documented normal state.

