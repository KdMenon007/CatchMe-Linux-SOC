# Project 02 — MITRE ATT&CK Mapping

## Overview

Project 02 investigates the abuse of a valid Linux account to access the `soc-linux` endpoint through SSH.

The observed activity was mapped to MITRE ATT&CK techniques based on the collected laboratory evidence.

### Lab Context

| Component | Value |
|---|---|
| Attacker | Kali |
| Attacker IP | `192.168.1.10` |
| Target | `soc-linux` |
| Target IP | `192.168.1.16` |
| Account | `socadmin` |
| Remote Service | SSH |
| Process | `sshd` |

---

# T1078 — Valid Accounts

## Technique

**T1078 — Valid Accounts**

## Description

The activity involved the use of valid credentials associated with the `socadmin` account to authenticate to the Linux endpoint.

## Evidence

A successful password-based SSH authentication was confirmed at:

```text
2026-10-02 08:36:22.295 IST
```

Linux SSH evidence:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

Associated Elastic telemetry:

```text
host.name  = soc-linux
source.ip  = 192.168.1.10
user.name  = socadmin
process.name = sshd
event.action = ssh_login
```

## Assessment

The observed activity directly supports **T1078 — Valid Accounts** because the `socadmin` account was successfully used for authentication.

## Evidence Screenshot

```text
09-Screenshots/MITRE/01-T1078-valid-accounts.png
```

---

# T1021.004 — Remote Services: SSH

## Technique

**T1021.004 — Remote Services: SSH**

## Description

The attacker used Secure Shell (SSH) to remotely access the Linux endpoint.

## Evidence

The observed connection path was:

```text
192.168.1.10
      |
      | SSH
      v
192.168.1.16
```

The target SSH service was:

```text
process.name = sshd
```

Elastic telemetry identified:

```text
source.ip = 192.168.1.10
host.name = soc-linux
user.name = socadmin
event.action = ssh_login
```

The Linux authentication log also confirmed password-based SSH authentication.

## Assessment

The evidence directly supports **T1021.004 — Remote Services: SSH**.

## Evidence Screenshot

```text
09-Screenshots/MITRE/02-T1021.004-ssh.png
```

---

# T1059.004 — Command and Scripting Interpreter: Unix Shell

## Technique

**T1059.004 — Command and Scripting Interpreter: Unix Shell**

## Description

A Unix shell was used during the controlled SSH session for system discovery and validation.

Observed controlled commands included:

```text
date
hostname
whoami
id
echo "$SSH_CONNECTION"
echo "$SSH_CLIENT"
pwd
ls -la
uname -a
cat /etc/os-release
ps -p $$ -o pid,ppid,user,comm,args
```

## Evidence

The observed shell process was:

```text
PID:      1738
PPID:     1737
USER:     socadmin
COMMAND:  bash
ARGS:     -bash
```

The activity occurred after SSH access to `soc-linux`.

## Assessment

The observed shell activity supports **T1059.004 — Command and Scripting Interpreter: Unix Shell**.

This is classified as supporting activity from the controlled laboratory session.

It is not being presented as evidence of additional malicious payload execution.

## Evidence Screenshot

```text
09-Screenshots/MITRE/03-T1059.004-unix-shell.png
```

---

# MITRE ATT&CK Mapping Summary

| Technique ID  | Technique                                     | Evidence Status              |
| ------------- | --------------------------------------------- | ---------------------------- |
| **T1078**     | Valid Accounts                                | Confirmed                    |
| **T1021.004** | Remote Services: SSH                          | Confirmed                    |
| **T1059.004** | Command and Scripting Interpreter: Unix Shell | Observed supporting activity |

---

# Attack Chain Mapping

```text
Valid Credentials
       |
       v
T1078 — Valid Accounts
       |
       v
SSH Remote Access
       |
       v
T1021.004 — Remote Services: SSH
       |
       v
Unix Shell Session
       |
       v
T1059.004 — Unix Shell
       |
       v
Elastic Telemetry
       |
       v
Detection
       |
       v
SOC Investigation
```

---

# Techniques Not Demonstrated

The investigation did not establish evidence for:

* Privilege escalation
* Root compromise
* SSH authorized-key persistence
* Credential dumping
* Malware execution
* Data exfiltration
* Destructive activity

These techniques are therefore not mapped to the observed incident.

---

# Evidence Screenshots

```text
09-Screenshots/MITRE/
├── 01-T1078-valid-accounts.png
├── 02-T1021.004-ssh.png
└── 03-T1059.004-unix-shell.png
```

---

# Conclusion

Project 02 maps the observed activity primarily to:

**T1078 — Valid Accounts**

and:

**T1021.004 — Remote Services: SSH**

The controlled post-authentication shell activity additionally supports:

**T1059.004 — Command and Scripting Interpreter: Unix Shell**

The MITRE mapping is restricted to techniques supported by the collected evidence.
