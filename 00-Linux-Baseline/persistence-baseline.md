# Persistence Baseline

## Purpose

This document records the normal persistence mechanisms identified on the CatchMe Linux SOC lab endpoint before security attack simulations begin.

The baseline provides a reference point for identifying unauthorized persistence mechanisms during future SOC investigations.

Primary persistence areas covered:

- User cron jobs
- System cron directories
- Systemd timers
- Enabled systemd units
- Security-sensitive system files

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

> The public version uses sanitized private lab addresses.

## User Cron Baseline

The current user has no personal crontab:

```text
no crontab for socadmin
```

This establishes the baseline for the `socadmin` account.

Any future user crontab should be reviewed for:

* Newly scheduled commands
* Scripts from unusual locations
* Commands running with elevated privileges
* Network connections
* Commands executed from temporary directories
* Persistence created after suspicious activity

## System Cron Baseline

### `/etc/cron.d`

The baseline contains:

```text
e2scrub_all
.placeholder
sysstat
```

### `/etc/cron.daily`

The baseline contains:

```text
apport
apt-compat
dpkg
logrotate
man-db
.placeholder
sysstat
```

### `/etc/cron.hourly`

The baseline contains:

```text
.placeholder
```

No scheduled executable was present in the hourly directory during baseline collection.

### `/etc/cron.weekly`

The baseline contains:

```text
man-db
.placeholder
```

### `/etc/cron.monthly`

The baseline contains:

```text
.placeholder
```

No scheduled executable was present in the monthly directory during baseline collection.

## Systemd Timer Baseline

The endpoint reported **17 timers** during baseline collection.

The observed timers include:

```text
sysstat-collect.timer
fwupd-refresh.timer
motd-news.timer
dpkg-db-backup.timer
logrotate.timer
sysstat-summary.timer
man-db.timer
apt-daily.timer
update-notifier-download.timer
systemd-tmpfiles-clean.timer
apt-daily-upgrade.timer
e2scrub_all.timer
fstrim.timer
update-notifier-motd.timer
apport-autoreport.timer
snapd.snap-repair.timer
ua-timer.timer
```

These timers represent the scheduled activity observed on the endpoint at baseline collection time.

Future investigations should compare timer names, schedules, associated services, and execution commands against this baseline.

## Enabled Systemd Units

The baseline identified **79 enabled unit files**.

Important enabled services observed include:

```text
apparmor.service
auditd.service
cron.service
elastic-agent.service
open-vm-tools.service
rsyslog.service
ssh.service
systemd-networkd.service
systemd-resolved.service
systemd-timesyncd.service
ufw.service
unattended-upgrades.service
vgauth.service
```

Enabled sockets include:

```text
apport-forward.socket
cloud-init-hotplugd.socket
dm-event.socket
iscsid.socket
lvm2-lvmpolld.socket
lxd-installer.socket
multipathd.socket
snapd.socket
ssh.socket
systemd-networkd.socket
uuidd.socket
```

Enabled target:

```text
remote-fs.target
```

Enabled timers include:

```text
apport-autoreport.timer
apt-daily-upgrade.timer
apt-daily.timer
dpkg-db-backup.timer
e2scrub_all.timer
fstrim.timer
fwupd-refresh.timer
logrotate.timer
man-db.timer
mdcheck_continue.timer
mdcheck_start.timer
mdmonitor-oneshot.timer
motd-news.timer
snapd.snap-repair.timer
sysstat-collect.timer
sysstat-summary.timer
ua-timer.timer
update-notifier-download.timer
update-notifier-motd.timer
```

## Security-Sensitive File Metadata

The following files were included in the baseline:

```text
/etc/passwd
/etc/shadow
/etc/group
/etc/sudoers
/etc/ssh/sshd_config
```

### `/etc/passwd`

```text
Permissions: 0644
Owner: root
Group: root
Size: 1662 bytes
```

### `/etc/shadow`

```text
Permissions: 0640
Owner: root
Group: shadow
Size: 931 bytes
```

The contents of `/etc/shadow` are **not included** in the public repository.

### `/etc/group`

```text
Permissions: 0644
Owner: root
Group: root
Size: 811 bytes
```

### `/etc/sudoers`

```text
Permissions: 0440
Owner: root
Group: root
Size: 1800 bytes
```

### `/etc/ssh/sshd_config`

```text
Permissions: 0644
Owner: root
Group: root
Size: 3517 bytes
```

## Persistence Locations

| Persistence Mechanism | Location                                           |
| --------------------- | -------------------------------------------------- |
| User cron             | User crontab                                       |
| System cron           | `/etc/cron.d/`                                     |
| Daily cron            | `/etc/cron.daily/`                                 |
| Hourly cron           | `/etc/cron.hourly/`                                |
| Weekly cron           | `/etc/cron.weekly/`                                |
| Monthly cron          | `/etc/cron.monthly/`                               |
| Systemd services      | `/etc/systemd/system/`, `/usr/lib/systemd/system/` |
| Systemd timers        | Systemd timer units                                |
| SSH persistence       | `~/.ssh/authorized_keys`                           |
| Sudo configuration    | `/etc/sudoers`, `/etc/sudoers.d/`                  |
| User accounts         | `/etc/passwd`                                      |
| Group membership      | `/etc/group`                                       |

## Persistence Investigation Points

| Change                                          | Investigation Relevance           |
| ----------------------------------------------- | --------------------------------- |
| New user crontab                                | Possible scheduled persistence    |
| New file in `/etc/cron.d/`                      | Possible system-wide persistence  |
| New executable in cron directories              | Possible scheduled execution      |
| New systemd service                             | Possible service persistence      |
| New systemd timer                               | Possible scheduled persistence    |
| Unexpected enabled unit                         | Possible persistence              |
| Modified SSH `authorized_keys`                  | Possible SSH persistence          |
| Modified sudo configuration                     | Possible privilege persistence    |
| Unexpected account creation                     | Possible account persistence      |
| Unexpected security-sensitive file modification | Possible persistence or tampering |

## SOC Investigation Correlation

Persistence activity should be correlated with:

* Process creation
* Command-line execution
* File creation
* File modification
* User activity
* Privilege escalation
* Authentication events
* SSH activity
* Auditd telemetry
* Network connections
* Elastic Agent telemetry
* Elastic SIEM alerts

Example investigation flow:

```text
Initial Access
      |
      v
Command Execution
      |
      v
Persistence Mechanism
      |
      +------------------+
      |                  |
      v                  v
Cron Job           Systemd Unit/Timer
      |                  |
      +--------+---------+
               |
               v
        Automatic Execution
               |
               v
        Process Activity
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
          Investigation
```
