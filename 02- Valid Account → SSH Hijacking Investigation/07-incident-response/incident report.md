# Incident Report

## Project 02 — Valid Account → SSH Hijacking

## 1. Incident Overview

| Field | Value |
|---|---|
| Project | CatchMe Linux SOC — Project 02 |
| Incident Type | Valid Account Abuse / SSH Activity |
| Target Host | `soc-linux` |
| Target IP | `192.168.1.16` |
| Source Host | Kali |
| Source IP | `192.168.1.10` |
| Account | `socadmin` |
| Service | SSH |
| Protocol | TCP |
| Port | `22` |
| Detection Platform | Elastic SIEM |
| Endpoint Telemetry | Elastic Agent / Linux authentication telemetry |
| Incident Status | Contained and Remediated |

---

## 2. Executive Summary

Project 02 simulated abuse of a valid Linux account through SSH in the controlled CatchMe laboratory environment.

The activity originated from the Kali attacker host `192.168.1.10` and targeted the Linux endpoint `soc-linux` at `192.168.1.16`.

The investigation correlated Linux SSH authentication logs, SSH process telemetry, Elastic SIEM events, source IP information, account information, and session activity.

Elastic detection logic generated an alert for SSH activity involving the `socadmin` account and the `sshd` process.

The response then progressed through containment and remediation.

The endpoint was subsequently hardened by:

- disabling SSH password authentication
- retaining validated SSH public-key authentication
- disabling X11 forwarding
- enabling UFW
- restricting SSH access to `192.168.1.0/24`
- validating SSH access after firewall activation

---

## 3. Incident Timeline

### 08:36 IST — SSH Authentication Activity

Linux authentication telemetry recorded SSH activity from:

```text
192.168.1.10
```

against:

```text
soc-linux
192.168.1.16
```

for:

```text
socadmin
```

A confirmed successful SSH authentication event was recorded at approximately:

```text
2026-10-02 08:36:22.295 IST
```

The corresponding event contained:

```text
event.action = ssh_login
process.name = sshd
host.name = soc-linux
source.ip = 192.168.1.10
source.port = 40598
user.name = socadmin
```

The underlying Linux authentication message confirmed:

```text
Accepted password for socadmin from 192.168.1.10
```

---

## 4. Detection

A custom Elastic detection rule was created:

```text
CatchMe - Linux SSH Valid Account Activity
```

Detection query:

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

Configuration:

| Setting     | Value          |
| ----------- | -------------- |
| Rule Type   | Custom Query   |
| Language    | KQL            |
| Severity    | Medium         |
| Risk Score  | 47             |
| Schedule    | Every 1 minute |
| Look-back   | 1 minute       |
| Suppression | Disabled       |

Fresh SSH activity was generated to validate the detection.

Elastic subsequently generated an alert.

The alert contained:

* `host.name = soc-linux`
* `process.name = sshd`
* `source.ip = 192.168.1.10`
* `user.name = socadmin`
* `event.action = ssh_login`
* Severity: Medium
* Risk Score: 47

Detection evidence is stored under:

```text
09-Screenshots/Detection/
```

---

## 5. Investigation

The investigation correlated the alert with the underlying SSH telemetry.

Primary investigation fields included:

```text
event.action
process.name
host.name
source.ip
source.port
user.name
```

The investigation also correlated Elastic telemetry with Linux-native evidence:

```text
/var/log/auth.log
journalctl -u ssh
```

The investigation identified SSH activity associated with:

```text
192.168.1.10
```

and:

```text
socadmin
```

The investigation did not rely on a single Elastic event. Authentication, SSH process, session, source, and user information were correlated.

Investigation evidence:

```text
09-Screenshots/Investigation/
├── 01-alert-investigation-overview.png
├── 02-original-ssh-login-event.png
├── 03-authentication-event-correlation.png
├── 04-ssh-session-activity.png
└── 05-investigation-source-user-correlation.png
```

---

## 6. Evidence Assessment

The following activity was demonstrated and supported by captured evidence:

| Activity                                     | Status    |
| -------------------------------------------- | --------- |
| SSH activity from Kali                       | Confirmed |
| `socadmin` account involved                  | Confirmed |
| Successful password-based SSH authentication | Confirmed |
| SSH telemetry in Elastic                     | Confirmed |
| SSH detection alert                          | Confirmed |
| Investigation correlation                    | Confirmed |
| SSH hardening                                | Confirmed |
| Password authentication disabled             | Confirmed |
| Public-key authentication                    | Confirmed |
| X11 forwarding disabled                      | Confirmed |
| UFW enabled                                  | Confirmed |
| SSH access after firewall activation         | Confirmed |

The following were **not demonstrated** as part of this project:

* Privilege escalation
* Persistence
* Credential theft
* Data exfiltration
* Destructive activity
* Host compromise beyond the controlled SSH access scenario

---

## 7. Containment

Containment focused on reducing the SSH attack surface while preserving controlled administrative access.

The following controls were applied:

### Password Authentication

Changed from:

```text
passwordauthentication yes
```

to:

```text
passwordauthentication no
```

Password-only SSH validation subsequently returned:

```text
Permission denied (publickey).
```

### Public-Key Authentication

Public-key authentication remained enabled and was successfully validated after the password-authentication change.

### X11 Forwarding

Changed from:

```text
x11forwarding yes
```

to:

```text
x11forwarding no
```

### Firewall

UFW was enabled with:

```text
Default: deny (incoming)
Default: allow (outgoing)
```

SSH was permitted from:

```text
192.168.1.0/24
```

---

## 8. Remediation

The final validated SSH configuration included:

```text
permitrootlogin without-password
pubkeyauthentication yes
passwordauthentication no
x11forwarding no
permituserenvironment no
```

The SSH service remained active.

The final firewall state was:

```text
Status: active
```

with:

```text
22/tcp ALLOW IN 192.168.1.0/24
```

The dedicated Project 02 SSH key remained functional after remediation.

Post-firewall validation returned:

```text
FIREWALL_SSH_TEST_SUCCESS
socadmin
soc-linux
```

---

## 9. MITRE ATT&CK Mapping

### T1078 — Valid Accounts

The scenario involved use of the legitimate `socadmin` account for SSH access.

### T1021.004 — Remote Services: SSH

SSH was the remote-access mechanism used against the Linux endpoint.

### T1059.004 — Unix Shell

Unix shell activity was observed during the controlled post-authentication validation.

The exact scope of each technique is documented in:

```text
06-mitre/mitre-attack.md
```

---

## 10. Detection and Response Workflow

The complete SOC workflow was:

```text
Valid Account Activity
        ↓
SSH Authentication
        ↓
Linux Authentication Telemetry
        ↓
Elastic Agent
        ↓
Elastic SIEM
        ↓
Threat Hunting
        ↓
Detection Rule
        ↓
Alert
        ↓
Investigation
        ↓
Containment
        ↓
Remediation
        ↓
Validation
```

---

## 11. Security Controls Applied

| Control                       | Result           |
| ----------------------------- | ---------------- |
| SSH password authentication   | Disabled         |
| SSH public-key authentication | Enabled          |
| X11 forwarding                | Disabled         |
| UFW                           | Enabled          |
| Incoming firewall policy      | Deny             |
| SSH network scope             | `192.168.1.0/24` |
| SSH service                   | Active           |
| Key-based SSH validation      | Successful       |
| Password-based SSH validation | Blocked          |

---

## 12. Evidence Location

### Attack

```text
09-Screenshots/Attack/
```

### Telemetry

```text
09-Screenshots/Telemetry/
```

### Detection

```text
09-Screenshots/Detection/
```

### Investigation

```text
09-Screenshots/Investigation/
```

### Incident Response

```text
09-Screenshots/Incident-Response/
```

Incident-response evidence contains the numbered containment and remediation screenshots:

```text
01-active-ssh-session-before-containment.png
02-active-ssh-session-after-containment.png
03-remediation-ssh-configuration-baseline.png
04-remediation-account-privileges-baseline.png
05-remediation-network-firewall-baseline.png
06-remediation-ssh-key-authentication-success.png
07-remediation-password-authentication-disabled.png
08-remediation-ssh-reload-success.png
09-remediation-password-authentication-blocked.png
10-remediation-key-authentication-verified.png
11-remediation-x11-forwarding-disabled.png
12-remediation-x11-forwarding-reload-verified.png
13-remediation-ufw-ssh-rule-added.png
14-remediation-ufw-rule-verified.png
15-remediation-ufw-enabled.png
16-remediation-ssh-after-ufw-verified.png
17-remediation-final-state.png
```

---

## 13. Lessons Learned

The project demonstrated several operational lessons:

1. Valid accounts require contextual monitoring.
2. Successful authentication should be correlated with source IP and session activity.
3. SIEM events should be validated against native Linux logs when event semantics are ambiguous.
4. Detection rules must be tested using fresh telemetry.
5. SSH hardening should preserve a validated administrative access path.
6. Firewall changes should be verified before enforcement.
7. Evidence should be captured during each stage rather than reconstructed later.
8. Incident documentation should distinguish confirmed observations from assumptions.

The complete lessons are documented in:

```text
07-incident-response/lessons-learned.md
```

---

## 14. Final Incident Assessment

The controlled Project 02 scenario demonstrated valid-account SSH activity against `soc-linux` and successfully exercised the complete SOC detection and response workflow.

The project confirmed:

* SSH authentication telemetry was collected.
* Elastic SIEM received the relevant telemetry.
* Threat hunting identified correlated activity.
* Detection logic generated an alert.
* The alert was investigated using underlying events.
* SSH security controls were hardened.
* Firewall enforcement was enabled.
* Key-based SSH access remained functional after remediation.

No successful privilege escalation, persistence, credential theft, data exfiltration, or destructive activity was demonstrated during the documented scenario.

---

## 15. Incident Status

```text
Detection       : COMPLETED
Investigation   : COMPLETED
Containment     : COMPLETED
Remediation     : COMPLETED
Validation      : COMPLETED
Documentation   : COMPLETED
```

**Project 02 Incident Response Status: COMPLETED**

