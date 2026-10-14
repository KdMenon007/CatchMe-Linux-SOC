# Environment Verification — Project 04

## Lab components
| Role | Host/IP |
|---|---|
| Attacker workstation | Kali Linux — `192.168.1.10` |
| Target | `soc-linux` — `192.168.1.16` |
| SIEM | Elastic — `192.168.1.11` |

## Target platform
- OS: Ubuntu 24.04.4 LTS (Noble Numbat)
- Architecture: `aarch64`
- Kernel: `6.8.0-146-generic`
- Kernel package observed: `6.8.0-146.146`
- Elastic Agent version observed in the lab: `9.5.5`
- Timezone for event reporting: `Asia/Kolkata` (IST, UTC+05:30)

## Accounts
- `p04ssh`: UID 1001; checked as the low-privilege account.
- `socadmin`: UID 1000; used for administrative baseline checks. The account belongs to the `sudo` group, but non-interactive `sudo -n -l` returned `sudo: a password is required`. This result alone does not establish the complete sudo policy.

## Verification commands
Run these only when a fresh baseline is required:

```bash
hostname
ip -brief address
cat /etc/os-release
uname -a
id
timedatectl
systemctl is-active elastic-agent
```

Record actual output and collection time in the evidence folder. Do not paste credentials, private keys, or secret environment values into the repository.

## Telemetry verification
Elastic process events were observed for `find`, `stat`, `grep`, `cat`, `dpkg-query`, `getcap`, and `sudo`. The available fields varied by event type. Some syscall events contained a process name but no command line or parent-process details.

## Safety checks
- Do not add new cron entries or alter system permissions on the protected target.
- Do not change sudoers, `/etc/passwd`, `/etc/shadow`, system executables, or SUID bits.
- Use a disposable VM/snapshot for any future exploit requiring intentional misconfiguration.
