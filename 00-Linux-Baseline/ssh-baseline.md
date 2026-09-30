# SSH Baseline

## Purpose

This document records the baseline SSH service state, listening configuration, effective SSH daemon configuration, SSH configuration files, and recent authentication activity for the CatchMe Linux SOC lab.

The baseline provides a reference point for detecting:

- SSH brute-force activity
- Unauthorized SSH access
- SSH configuration changes
- Authentication-method changes
- SSH persistence
- Unexpected SSH listeners
- Root login changes
- SSH service tampering

## Lab Environment

| Field | Value |
|---|---|
| Hostname | `soc-linux` |
| IP Address | `192.168.56.20` |
| Operating System | Ubuntu 24.04.4 LTS |
| Architecture | `aarch64` |
| SSH Service | OpenSSH |
| SIEM | `catchme-siem` |
| SIEM IP | `192.168.56.10` |
| Network | Private CatchMe Lab |

> The public version uses sanitized private lab addresses.

## SSH Service Status

The SSH service was active and running during baseline collection.

```text
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; disabled; preset: enabled)
     Active: active (running)
     TriggeredBy: ● ssh.socket
     Main PID: 1486 (sshd)
```
### Service State

| Attribute          | Baseline           |
| ------------------ | ------------------ |
| Service            | `ssh.service`      |
| Status             | `active (running)` |
| Main Process       | `sshd`             |
| Main PID           | `1486`             |
| Socket Activation  | `ssh.socket`       |
| Service Enablement | `disabled`         |
| Preset             | `enabled`          |

The SSH service is activated through `ssh.socket` in the current lab configuration.

## SSH Listening State

SSH was listening on TCP port `22` on both IPv4 and IPv6:

```text
LISTEN 0      4096         0.0.0.0:22         0.0.0.0:*    users:(("sshd",pid=1486,fd=3),("systemd",pid=1,fd=102))
LISTEN 0      4096            [::]:22            [::]:*    users:(("sshd",pid=1486,fd=4),("systemd",pid=1,fd=103))
```

### Listening Configuration

| Protocol | Listen Address | Port | Process |
| -------- | -------------- | ---: | ------- |
| IPv4     | `0.0.0.0`      | `22` | `sshd`  |
| IPv6     | `::`           | `22` | `sshd`  |

This means the SSH service is listening on all available IPv4 and IPv6 interfaces in the current lab configuration.

## Effective SSH Configuration

The effective SSH daemon configuration was collected using `sshd -T`.

```text
port 22
listenaddress [::]:22
listenaddress 0.0.0.0:22
maxauthtries 6
permitrootlogin without-password
pubkeyauthentication yes
passwordauthentication yes
permitemptypasswords no
```

### Configuration Summary

| Parameter                | Baseline Value     |
| ------------------------ | ------------------ |
| `port`                   | `22`               |
| `listenaddress`          | `[::]:22`          |
| `listenaddress`          | `0.0.0.0:22`       |
| `maxauthtries`           | `6`                |
| `permitrootlogin`        | `without-password` |
| `pubkeyauthentication`   | `yes`              |
| `passwordauthentication` | `yes`              |
| `permitemptypasswords`   | `no`               |

These values form the SSH configuration baseline for future investigations.

## SSH Configuration Directory

The `/etc/ssh/` directory contained:

```text
drwxr-xr-x   4 root root   4096 Sep 25 05:45 .
drwxr-xr-x 107 root root   4096 Sep 25 05:46 ..
-rw-r--r--   1 root root 620042 Jul  9 18:16 moduli
-rw-r--r--   1 root root   1649 Aug 26  2025 ssh_config
drwxr-xr-x   2 root root   4096 Aug 26  2025 ssh_config.d
-rw-r--r--   1 root root   3517 Jul  9 18:16 sshd_config
drwxr-xr-x   2 root root   4096 Sep  2 17:53 sshd_config.d
-rw-------   1 root root    505 Sep  2 17:53 ssh_host_ecdsa_key
-rw-r--r--   1 root root    176 Sep  2 17:53 ssh_host_ecdsa_key.pub
-rw-------   1 root root    411 Sep  2 17:53 ssh_host_ed25519_key
-rw-r--r--   1 root root     96 Sep  2 17:53 ssh_host_ed25519_key.pub
-rw-------   1 root root   2602 Sep  2 17:53 ssh_host_rsa_key
-rw-r--r--   1 root root    568 Sep  2 17:53 ssh_host_rsa_key.pub
-rw-r--r--   1 root root    342 Dec  7  2020 ssh_import_id
```

### Public Repository Security

The directory listing confirms that SSH host private keys exist on the endpoint.

The **private-key contents are intentionally not included in this public documentation**.

Only the existence, permissions, and filenames of the SSH host-key files are documented.

## SSH Daemon Configuration File

The primary SSH daemon configuration file is:

```text
/etc/ssh/sshd_config
```

Baseline metadata:

```text
File: /etc/ssh/sshd_config
Size: 3517 bytes
Owner: root
Group: root
Permissions: 0644
```

The file modification timestamp recorded during collection was:

```text
Modify: 2026-07-09 18:16:38 UTC
```

The configuration file metadata can be used as a reference during future investigations.

## SSH Authentication Baseline

Recent SSH activity during the baseline collection period showed successful authentication for the lab administrative account.

Sanitized baseline activity:

```text
Accepted password for socadmin from <LAB_SOURCE_IP>
Accepted password for socadmin from <LAB_SOURCE_IP>
Accepted password for socadmin from <LAB_SOURCE_IP>
```

One authentication attempt failed before a successful password authentication:

```text
authentication failure for socadmin from <LAB_SOURCE_IP>
Failed password for socadmin from <LAB_SOURCE_IP>
Accepted password for socadmin from <LAB_SOURCE_IP>
```

This activity occurred during legitimate lab administration and should therefore be treated as **baseline activity**, not as an attack finding.

## Authentication Baseline Summary

| Activity                                | Baseline                         |
| --------------------------------------- | -------------------------------- |
| Successful SSH authentication           | Observed                         |
| Authentication method                   | Password                         |
| Account                                 | `socadmin`                       |
| Failed authentication                   | Observed once                    |
| Successful authentication after failure | Observed                         |
| Root SSH authentication                 | Not observed                     |
| Unexpected user authentication          | Not observed in collected output |

## SSH Investigation Points

Future deviations from this baseline should be investigated for:

* Repeated failed authentication attempts
* Successful authentication after multiple failures
* Authentication from unexpected source IPs
* Authentication using unexpected accounts
* Root SSH authentication
* Unexpected SSH keys
* New `authorized_keys` entries
* SSH configuration modifications
* Changes to authentication methods
* Unexpected SSH service restarts
* Unexpected SSH listening ports

## SSH Persistence Investigation

SSH persistence investigations should examine:

```text
~/.ssh/
~/.ssh/authorized_keys
/etc/ssh/
```

Important investigation points include:

* New `authorized_keys` entries
* Unexpected public keys
* Modified SSH daemon configuration
* New SSH users
* Changes to SSH authentication methods
* Unexpected SSH service restarts

Private key contents must not be committed to the public repository.

## SOC Investigation Correlation

SSH activity should be correlated with:

* Authentication events
* Auditd events
* Process execution
* Command-line activity
* User identity
* Source IP
* Destination IP
* Destination port
* File modifications
* `authorized_keys` changes
* Elastic Agent telemetry
* Elastic SIEM alerts

Example correlation:

```text
SSH Authentication
        |
        v
Source IP
        |
        v
User Account
        |
        v
Process / Command Line
        |
        v
File Changes
        |
        v
Privilege Activity
        |
        v
Network Activity
        |
        v
Elastic SIEM
        |
        v
Threat Hunting
        |
        v
Incident Investigation
```
