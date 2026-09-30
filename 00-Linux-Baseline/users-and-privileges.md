# Users and Privileges Baseline

## Purpose

This document records the baseline identity, local user accounts, groups, sudo configuration, sudo access, and UID 0 accounts for the CatchMe Linux SOC lab.

The baseline provides a reference point for detecting changes during future security investigations.

## Lab Environment

| Field | Value |
|---|---|
| Hostname | `soc-linux` |
| IP Address | `192.168.56.20` |
| Operating System | Ubuntu 24.04.4 LTS |
| Architecture | `aarch64` |
| SIEM | `catchme-siem` |
| SIEM IP | `192.168.56.10` |
| Network | Private CatchMe Lab |

> The addresses and identities documented in this public version are sanitized lab values.

## Current User

The baseline user is represented as:

```text
uid=1000(socadmin) gid=1000(socadmin) groups=1000(socadmin),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),101(lxd)
```

### Privilege Summary

| Attribute         | Value                                   |
| ----------------- | --------------------------------------- |
| Username          | `socadmin`                              |
| UID               | `1000`                                  |
| Primary GID       | `1000`                                  |
| Primary Group     | `socadmin`                              |
| Sudo Group        | `sudo`                                  |
| Additional Groups | `adm`, `cdrom`, `dip`, `plugdev`, `lxd` |
| Login Shell       | `/bin/bash`                             |

The `socadmin` account is the administrative account used for the CatchMe Linux SOC lab.

## Local User Accounts

The baseline identified the following local accounts:

| Username           |   UID |   GID | Login Shell         |
| ------------------ | ----: | ----: | ------------------- |
| `root`             |     0 |     0 | `/bin/bash`         |
| `daemon`           |     1 |     1 | `/usr/sbin/nologin` |
| `bin`              |     2 |     2 | `/usr/sbin/nologin` |
| `sys`              |     3 |     3 | `/usr/sbin/nologin` |
| `sync`             |     4 | 65534 | `/bin/sync`         |
| `games`            |     5 |    60 | `/usr/sbin/nologin` |
| `man`              |     6 |    12 | `/usr/sbin/nologin` |
| `lp`               |     7 |     7 | `/usr/sbin/nologin` |
| `mail`             |     8 |     8 | `/usr/sbin/nologin` |
| `news`             |     9 |     9 | `/usr/sbin/nologin` |
| `uucp`             |    10 |    10 | `/usr/sbin/nologin` |
| `proxy`            |    13 |    13 | `/usr/sbin/nologin` |
| `www-data`         |    33 |    33 | `/usr/sbin/nologin` |
| `backup`           |    34 |    34 | `/usr/sbin/nologin` |
| `list`             |    38 |    38 | `/usr/sbin/nologin` |
| `irc`              |    39 |    39 | `/usr/sbin/nologin` |
| `_apt`             |    42 | 65534 | `/usr/sbin/nologin` |
| `nobody`           | 65534 | 65534 | `/usr/sbin/nologin` |
| `systemd-network`  |   998 |   998 | `/usr/sbin/nologin` |
| `systemd-timesync` |   997 |   997 | `/usr/sbin/nologin` |
| `dhcpcd`           |   100 | 65534 | `/bin/false`        |
| `messagebus`       |   101 |   102 | `/usr/sbin/nologin` |
| `systemd-resolve`  |   992 |   992 | `/usr/sbin/nologin` |
| `pollinate`        |   102 |     1 | `/bin/false`        |
| `polkitd`          |   991 |   991 | `/usr/sbin/nologin` |
| `syslog`           |   103 |   104 | `/usr/sbin/nologin` |
| `uuidd`            |   104 |   105 | `/usr/sbin/nologin` |
| `tcpdump`          |   105 |   107 | `/usr/sbin/nologin` |
| `landscape`        |   106 |   108 | `/usr/sbin/nologin` |
| `fwupd-refresh`    |   989 |   989 | `/usr/sbin/nologin` |
| `socadmin`         |  1000 |  1000 | `/bin/bash`         |
| `sshd`             |   107 | 65534 | `/usr/sbin/nologin` |

## Groups

The baseline includes the following local groups:

```text
root
daemon
bin
sys
adm
tty
disk
lp
mail
news
uucp
man
proxy
kmem
dialout
fax
voice
cdrom
floppy
tape
sudo
audio
dip
www-data
backup
operator
list
irc
src
shadow
utmp
video
sasl
plugdev
staff
games
users
nogroup
systemd-journal
systemd-network
systemd-timesync
input
sgx
kvm
render
lxd
messagebus
systemd-resolve
_ssh
polkitd
crontab
syslog
uuidd
rdma
tcpdump
landscape
fwupd-refresh
socadmin
```

## Sudo Configuration

The active sudo configuration baseline contains:

```text
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
Defaults        use_pty

root    ALL=(ALL:ALL) ALL
%admin ALL=(ALL) ALL
%sudo   ALL=(ALL:ALL) ALL

@includedir /etc/sudoers.d
```

## Current Sudo Access

The `socadmin` account has full administrative sudo access in the lab:

```text
User socadmin may run the following commands on soc-linux:
    (ALL : ALL) ALL
```

This is expected for the administrative account used to operate the security lab.

## UID 0 Accounts

The baseline identified:

```text
root:0:/bin/bash
```

Only the standard `root` account was identified with UID `0`.

Any unexpected additional UID 0 account discovered during a future investigation should be investigated as a potential privilege-escalation or persistence event.

## Security Monitoring Points

The following changes should trigger investigation:

| Change                         | SOC Investigation Relevance                       |
| ------------------------------ | ------------------------------------------------- |
| New local user                 | Possible persistence or unauthorized access       |
| Unexpected UID 0 account       | Possible privilege escalation or persistence      |
| New sudo permission            | Possible privilege escalation                     |
| Modified sudoers configuration | Possible administrative access modification       |
| Unexpected group membership    | Possible privilege escalation                     |
| New administrative account     | Possible persistence                              |
| Unexpected login shell         | Possible account modification                     |
| Unexpected sudo execution      | Possible administrative abuse                     |
| Disabled or deleted account    | Possible attacker cleanup or account manipulation |

## Investigation Correlation

User and privilege activity should be correlated with:

* Authentication events
* SSH activity
* Sudo execution
* Process creation
* Command-line activity
* Auditd events
* File modifications
* Group membership changes
* Elastic Agent telemetry
* Elastic SIEM alerts

Example investigation flow:

```text
User / Account Change
        |
        v
Authentication Activity
        |
        v
Privilege Assignment
        |
        v
Sudo / Administrative Execution
        |
        v
Process Execution
        |
        v
File / Configuration Change
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
