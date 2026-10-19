
---

## 🧭 **Big Picture**

Linux logs are mostly plain text in `/var/log`. No Event Viewer, no neat Event IDs — _you parse what you get._ SOC analysts use filtering (`grep`), log searching, and runtime monitoring (like **auditd**) to catch malicious activity.

---

## 📂 **Where Logs Live**

- **Main log folder:** `/var/log`
    
- Important ones:
    
    |File|Purpose|Typical Use|
    |---|---|---|
    |`/var/log/syslog` / `/var/log/messages`|General system events|Broad system behavior|
    |`/var/log/auth.log` / `/var/log/secure`|Logins, sudo, SSH, user changes|Authentication investigations (_most important_)|
    |`/var/log/kern.log`|Kernel messages|Advanced OS troubleshooting|
    |`/var/log/dpkg.log` / `/var/log/apt`|Package installs & updates|Malware persistence / unauthorized software checks|
    |`/var/log/nginx/access.log`|Web access logs|Web attacks, enumeration, brute force|
    

---

## 🔍 **Searching & Filtering Logs**

Linux logs are text → use `grep` like your sword.

Examples:

```
grep CRON /var/log/syslog
grep -R "login" /var/log
grep -E 'session opened|session closed' /var/log/auth.log
```

---

## 👤 **Authentication Logs `/var/log/auth.log`**

Tracks:

- Local login
    
- SSH login attempts
    
- `sudo` & privilege escalation
    
- User management commands
    

![[Pasted image 20251111090635.png]]

### **Login Example**

```
session opened for user bob(uid=1001)
session closed for user bob
```

### **SSH Brute Force Patterns**

```
Failed password for root from <IP>
Accepted publickey for bob from <IP>
```

---

## 🧍 **User Management Detection**

Search for:

```
grep -E '(useradd|userdel|usermod|passwd)' /var/log/auth.log
```

Attack signals:

- New unknown user created
    
- User added to `sudo` group
    
- Password changed after suspicious login
    

---

## 🔐 **Sudo Activity**

```
grep 'COMMAND=' /var/log/auth.log
```

Shows what was done with admin privileges.

Example abuse:

```
systemctl stop edr
ufw status
sudo su
```

---

## 🌐 **Web Server Logs**

**Nginx access.log format:**

```
<IP> <timestamp> "GET /login" 200 <size>
```

Useful for:

- Bruteforce
    
- LFI/RFI attempts
    
- Admin panel access
    

---

## 🧾 **Bash History**

- Stored at: `~/.bash_history`
    
- Viewed with: `history`
    

**BUT LIMITATIONS:**

|Weakness|How attacker bypasses|
|---|---|
|Commands not logged until logout|Kill session or crash it|
|Leading space omits logging|`echo attack.sh`|
|Use another shell|`/bin/sh`|
|Use scripts instead of typing|`./evil.sh`|

→ **So bash history is helpful, not reliable.**

---

## ⚙️ **Runtime Monitoring (The Real Game Changer)**

![[Pasted image 20251111090655.png]]

Linux _does NOT_ log:

- Process creation
    
- File modifications
    
- Network connections
    

So we use **auditd** (or Sysmon for Linux, Falco, EDRs) to catch system calls — the core OS actions.

![[Pasted image 20251111090716.png]]

---

## 🛰️ **Auditd Basics**

Rules stored in:

```
/etc/audit/rules.d/
```

View logs:

```
ausearch -i -k <keyword>
```

### Example: Detect wget execution

```
ausearch -i -k proc_wget
```

Shows:

- Command run (`proctitle`)
    
- Directory (`cwd`)
    
- Executed file (`exe`)
    
- User who logged in (`auid`)
    
- User who actually ran it (`uid`)
    

![[Pasted image 20251111090735.png]]

---

## 📁 **File Modification Monitoring**

Example rule: track changes to `/etc/ssh/sshd_config`

```
ausearch -i -k file_sshconf
```

If `nano` or `sed` touched it → you'll see it.

---

## 🔄 **Alternative Runtime Tools**

|Tool|Best For|
|---|---|
|Sysmon for Linux|Windows-SOC teams transitioning smoothly|
|Falco|Container & Kubernetes environments|
|Osquery|Query OS like a database|
|EDRs|Enterprise-grade monitoring|

All of them rely on _system calls_ → same core principle.

---

## 🎯 **Final TL;DR**

If you remember anything, remember this:

|Layer|What You See|What You Catch|
|---|---|---|
|Syslog + Auth.log|Logins, sudo, SSH, services|Basic intrusion detection|
|App Logs|Web access, DB queries|Web attacks, insider misuse|
|Bash History|User-typed commands|_Partial visibility_|
|Auditd / Sysmon / Falco|System calls (process, files, network)|Full attacker behavior tree|

→ **Runtime monitoring complete the picture.** Without it, you're blind to most attacks.

---
