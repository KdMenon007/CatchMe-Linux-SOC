# Project 01 — Pre-Attack Baseline Check

## Purpose

Before executing the SSH brute-force simulation, the CatchMe Linux SOC environment was verified and a baseline was established.

The purpose of the baseline is to distinguish:

- Normal system activity
- Existing authentication activity
- Expected network services
- Existing SSH configuration
- Existing users and privileges
- Existing persistence mechanisms
- Existing telemetry
- Attack-generated activity

This prevents normal activity from being incorrectly attributed to the simulated attack.

---

# 1. Lab Environment

| Component | Hostname | IP Address | Role |
|---|---|---|---|
| Kali Linux | `kiran` | `192.168.1.10` | Attacker |
| Linux Endpoint | `soc-linux` | `192.168.1.16` | Target |
| Elastic SIEM | `elastic-siem` | `192.168.1.11` | SIEM / Fleet |
| Gateway | — | `192.168.1.1` | Network Gateway |
| Network | — | `192.168.1.0/24` | Lab Network |

---

# 2. Linux Endpoint Baseline

## Host Identity

```text
Hostname:
soc-linux
```

Operating system:

```text
Ubuntu 24.04.4 LTS
```

Kernel:

```text
6.8.0-142-generic
```

Architecture:

```text
ARM64 / aarch64
```

The endpoint is the controlled Linux victim used for Project 01.

---

# 3. Network Baseline

The Linux endpoint was verified on:

```text
192.168.1.16/24
```

Gateway:

```text
192.168.1.1
```

Elastic SIEM:

```text
192.168.1.11
```

Attacker:

```text
192.168.1.10
```

The endpoint could communicate with the Elastic SIEM before the attack.

---

# 4. SSH Baseline

SSH was confirmed to be active before the simulation.

Service:

```text
ssh.service
```

Listening service:

```text
TCP/22
```

Observed listeners:

```text
0.0.0.0:22
[::]:22
```

SSH configuration baseline:

```text
MaxAuthTries: 6
PermitRootLogin: without-password
PubkeyAuthentication: yes
PasswordAuthentication: yes
PermitEmptyPasswords: no
```

The SSH service therefore provided the remote authentication surface used by Project 01.

---

# 5. Existing Authentication Activity

Before the attack, existing SSH authentication activity was reviewed.

The baseline included legitimate administrative sessions involving:

```text
User:
socadmin
```

and:

```text
Source:
192.168.1.10
```

These sessions occurred before the controlled Hydra test.

Therefore, they were treated as legitimate baseline activity and were **not attributed to the brute-force simulation**.

This distinction is important because the attacker system and the legitimate administration source were the same lab machine.

---

# 6. Pre-Attack SSH Journal

Before attack execution, the recent SSH journal was checked.

The immediate pre-attack query showed no recent SSH service entries in the selected baseline window.

This established a clean observation window before the controlled attack.

---

# 7. Failed Authentication Baseline

The failed-login database was also checked before the attack.

```text
lastb -n 10
```

No displayed failed-login activity was attributed to the immediate pre-attack observation window.

The `btmp` database existed and had a recent creation/start timestamp, but no prior displayed failed-login records were used as Project 01 attack evidence.

---

# 8. User Baseline

The primary administrative account used during the project was:

```text
socadmin
```

UID:

```text
1000
```

The account belonged to administrative groups including:

```text
sudo
adm
```

The only UID 0 account identified in the baseline was:

```text
root
```

No additional UID 0 account was used as part of the attack simulation.

---

# 9. Sudo Baseline

The endpoint's sudo configuration was reviewed before attack execution.

The baseline included:

```text
env_reset
mail_badpass
secure_path
use_pty
```

The `socadmin` account had administrative sudo access.

No sudo privilege escalation was performed during Project 01.

---

# 10. Persistence Baseline

The user's personal crontab was checked.

Result:

```text
No crontab for socadmin
```

Systemd timers were also reviewed.

The endpoint contained normal system-managed timers and enabled system services.

No Project 01 persistence mechanism was installed before the attack.

---

# 11. Running Services Baseline

Running system services were reviewed before attack execution.

Important expected services included:

```text
auditd
cron
dbus
elastic-agent
getty
open-vm-tools
polkit
rsyslog
ssh
systemd-journald
systemd-logind
systemd-networkd
systemd-resolved
systemd-timesyncd
systemd-udevd
udisks2
unattended-upgrades
```

No failed systemd services were identified during the baseline.

---

# 12. Auditd Baseline

Auditd was active before the attack.

Baseline status:

```text
enabled = 1
failure = 1
rate_limit = 0
backlog_limit = 8192
lost = 0
backlog = 0
```

No custom Auditd rules were present at the time of the baseline.

The absence of lost Auditd events was recorded as part of the telemetry baseline.

---

# 13. Elastic Agent Baseline

Elastic Agent was verified before attack execution.

Expected state:

```text
Fleet:
HEALTHY / Connected

Elastic Agent:
HEALTHY / Running
```

Agent version:

```text
9.5.2
```

Fleet Server:

```text
https://elastic-siem:8220
```

SIEM host:

```text
192.168.1.11
```

The endpoint maintained an established Elastic Agent connection to Fleet Server.

---

# 14. Fleet Connectivity Baseline

The Fleet Server API was tested from the Linux endpoint.

Expected response:

```text
HTTP 200
```

Fleet status:

```json
{"name":"fleet-server","status":"HEALTHY"}
```

This confirmed that the endpoint could communicate with the CatchMe Fleet infrastructure before the attack.

---

# 15. Endpoint → SIEM Telemetry Baseline

The baseline established the expected telemetry path:

```text
Linux Endpoint
     ↓
SSH / System / Auditd Telemetry
     ↓
Elastic Agent
     ↓
Fleet Server
     ↓
Elasticsearch
     ↓
Kibana / Elastic Security
```

The objective was to ensure that telemetry was functioning before generating suspicious activity.

---

# 16. Attacker Baseline

Kali was verified before attack execution.

Hostname:

```text
kiran
```

Fixed project attacker address:

```text
192.168.1.10
```

The Kali system also had another interface address in the lab environment, but:

```text
192.168.1.10
```

was used as the fixed Project 01 attacker address.

---

# 17. SSH Connectivity Test

Before launching Hydra, TCP/22 connectivity from Kali to the Linux endpoint was verified.

Target:

```text
192.168.1.16:22
```

Result:

```text
TCP connection successful
```

This confirmed that the attack path was reachable before the simulation.

---

# 18. Baseline Summary

| Area                          | Baseline Result                 |
| ----------------------------- | ------------------------------- |
| Hostname                      | `soc-linux`                     |
| OS                            | Ubuntu 24.04.4 LTS              |
| Kernel                        | `6.8.0-142-generic`             |
| Endpoint IP                   | `192.168.1.16`                  |
| SIEM IP                       | `192.168.1.11`                  |
| Attacker IP                   | `192.168.1.10`                  |
| SSH                           | Listening on TCP/22             |
| SSH authentication            | Password authentication enabled |
| Root account                  | Normal `root` account only      |
| `socadmin` crontab            | None                            |
| Auditd                        | Enabled                         |
| Auditd lost events            | 0                               |
| Elastic Agent                 | Healthy                         |
| Fleet connection              | Healthy                         |
| Fleet API                     | HTTP 200                        |
| Recent failed-login baseline  | No attack activity attributed   |
| SSH immediate baseline window | No entries                      |
| Attack activity               | Not yet started                 |

---

# 19. Baseline Integrity

The following activities were intentionally kept separate from the attack evidence:

```text
Legitimate administrative SSH sessions
        ≠
Hydra brute-force activity
```

The Project 01 investigation therefore uses the isolated attack-test window rather than treating every SSH event from `192.168.1.10` as malicious.

The final detection-test investigation window was:

```text
08:55 → 09:00 IST
```

This excluded the earlier test activity and allowed the final alert to be correlated with the second controlled Hydra execution.

---

# 20. Baseline Conclusion

The Linux endpoint was operational before attack execution.

The following components were available for observation:

```text
SSH
  ↓
Authentication Logs
  ↓
Auditd
  ↓
Elastic Agent
  ↓
Fleet
  ↓
Elastic SIEM
```

The baseline confirmed:

```text
Endpoint reachable
SSH reachable
Telemetry operational
Elastic Agent healthy
Fleet healthy
No Project 01 persistence
No successful attack activity before test window
```

The environment was therefore ready for controlled SSH password-guessing simulation.

---

## Evidence

Baseline evidence was collected under:

```text
00-pre-attack/
```

Relevant evidence categories:

```text
System information
Network state
SSH configuration
Running services
Users and privileges
Persistence
Auditd
Elastic Agent
Fleet connectivity
```

---

## Next Stage

```text
BASELINE
   ↓
01-ATTACK
   ↓
CONTROLLED SSH PASSWORD-GUESSING SIMULATION
```

Project 01 does not claim successful compromise because the actual test produced:

```text
0 valid password found
```

The attack stage will document only the activity that was actually executed and observed.


