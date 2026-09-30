# Services Baseline

## Purpose

This document records the normal service state of the CatchMe Linux SOC endpoint before security attack simulations begin.

The baseline is used during future investigations to identify:

- Unexpected services
- Newly installed services
- Services started by attackers
- Suspicious service modifications
- Unexpected service failures
- Persistence through systemd services
- Changes to enabled services

## Endpoint

| Field | Value |
|---|---|
| Hostname | `soc-linux` |
| IP Address | `192.168.1.16` |
| Operating System | Ubuntu 24.04.4 LTS |
| Kernel | `6.8.0-142-generic` |
| Architecture | `aarch64` |
| SIEM | Elastic SIEM |
| SIEM IP | `192.168.1.11` |

## Running Services

The baseline identified **21 loaded running services**.

| Service | Description |
|---|---|
| `auditd.service` | Security Auditing Service |
| `cron.service` | Regular background program processing daemon |
| `dbus.service` | D-Bus System Message Bus |
| `elastic-agent.service` | Elastic Agent |
| `getty@tty1.service` | Getty on tty1 |
| `ModemManager.service` | Modem Manager |
| `multipathd.service` | Device-Mapper Multipath Device Controller |
| `open-vm-tools.service` | VMware virtual machine tools |
| `polkit.service` | Authorization Manager |
| `rsyslog.service` | System Logging Service |
| `ssh.service` | OpenBSD Secure Shell server |
| `systemd-journald.service` | Journal Service |
| `systemd-logind.service` | User Login Management |
| `systemd-networkd.service` | Network Configuration |
| `systemd-resolved.service` | Network Name Resolution |
| `systemd-timesyncd.service` | Network Time Synchronization |
| `systemd-udevd.service` | Rule-based Manager for Device Events and Files |
| `udisks2.service` | Disk Manager |
| `unattended-upgrades.service` | Unattended Upgrades Shutdown |
| `user@1000.service` | User Manager for UID 1000 |
| `vgauth.service` | VMware Authentication Service |

## Enabled Unit Files

The baseline identified **79 enabled unit files**.

### Services

```text
apport-autoreport.path
apparmor.service
apport.service
auditd.service
blk-availability.service
cloud-config.service
cloud-final.service
cloud-init-local.service
cloud-init.service
console-setup.service
cron.service
dmesg.service
e2scrub_reap.service
elastic-agent.service
finalrd.service
getty@.service
grub-common.service
grub-initrd-fallback.service
keyboard-setup.service
lvm2-monitor.service
ModemManager.service
multipathd.service
networkd-dispatcher.service
open-iscsi.service
open-vm-tools.service
pollinate.service
rsyslog.service
secureboot-db.service
setvtrgb.service
snapd.apparmor.service
snapd.autoimport.service
snapd.core-fixup.service
snapd.recovery-chooser-trigger.service
snapd.seeded.service
snapd.service
snapd.system-shutdown.service
sysstat.service
systemd-networkd-wait-online.service
systemd-networkd.service
systemd-pstore.service
systemd-resolved.service
systemd-timesyncd.service
ua-reboot-cmds.service
ubuntu-advantage.service
udisks2.service
ufw.service
unattended-upgrades.service
vgauth.service
```

### Enabled Sockets

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

### Enabled Targets

```text
remote-fs.target
```

### Enabled Timers

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

## Failed Services

The baseline returned:

```text
UNIT LOAD ACTIVE SUB DESCRIPTION
```

No failed services were reported in the collected output.

## Security Baseline

The following service changes should be treated as investigation points during future SOC projects:

| Change                                 | Investigation Relevance                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| New running service                    | Possible software installation, exploitation, or persistence |
| Newly enabled service                  | Possible systemd persistence                                 |
| Unexpected service restart             | Possible attacker activity or configuration change           |
| Unknown service binary                 | Possible malicious executable                                |
| Service running as `root`              | Increased impact if compromised                              |
| Unexpected network-facing service      | Potential attack surface expansion                           |
| Failed security-related service        | Possible tampering or defensive-control disruption           |
| Modified service configuration         | Possible persistence or execution change                     |
| Service started from unusual location  | Possible malicious execution                                 |
| Service with unexpected parent process | Possible abnormal execution chain                            |

## SOC Investigation Correlation

During an investigation, service activity should be correlated with:

* Process creation
* Command-line execution
* Authentication events
* Auditd events
* File modifications
* Network connections
* Listening ports
* User activity
* Elastic Agent telemetry
* Elastic SIEM alerts

Example investigation chain:

```text
Service Change
      |
      v
Process Execution
      |
      v
Command Line
      |
      v
File / Configuration Change
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

