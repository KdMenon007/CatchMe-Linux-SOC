
---

## 🌐 Why SSH Gets Attacked So Much

SSH is everywhere. Like… _nearly every Linux server on the internet_ everywhere.  
And lots of admins still treat security like an optional side quest.

**What makes SSH risky?**

- Public-facing service = visible to everyone (Shodan sees ~40M exposed).
    
- Many use **password auth**, and those passwords are often embarrassingly weak.
    
- Key files get stolen through:
    
    - Leaked Git repos
        
    - Compromised admin laptops
        
    - Bad automation hygiene
        

**MITRE Technique:**  
**T1133 – External Remote Services**

![[Pasted image 20251111094442.png]]

---

## 🚪 Common SSH Breach Scenarios

|Method|How It Happens|Why It Works|
|---|---|---|
|Weak password brute-force|Botnets spray passwords at scale|People still use `123456`, bro|
|Stolen SSH private key|Key was stored somewhere dumb|Developers treat home directories like trash cans|
|Vulnerable SSH server|Old/wrongly configured SSH version|Nobody patches until headlines hit|

**Key log source:**  
`/var/log/auth.log` → Look for **Accepted password** or unusual IPs

```
cat /var/log/auth.log | grep "sshd"
```

---

## 🎯 Spotting Malicious SSH Logins

Look at **successful** logins — because the attacker only needs _one_.

Ask four questions:

|Question|Why It Matters|
|---|---|
|Who logged in?|Rogue account? Shared admin account?|
|From where?|Internal trusted IP or sketchy foreign cloud host?|
|When?|Does the user _usually_ log in at 3AM?|
|How?|Password login = always more suspicious than key login|

**Red flags:**

- `Accepted password` instead of `Accepted publickey`
    
- External IPs (check with threat intel)
    
- Odd login times
    

---

## 🌍 Other Linux Initial Access Vectors (Beyond SSH)

**MITRE Technique:**  
**T1190 – Exploit Public-Facing Applications**

Examples:

|Service|What Goes Wrong|Result|
|---|---|---|
|Zimbra Email Server|CVE allows remote code execution|Full system compromise|
|Docker API open to internet|No auth = remote container control|Crypto miners, botnets, chaos|
|WordPress Plugins|Malicious uploads/web shells|Shell access → root → game over|

---

## 🕸️ Web App Command Injection Example

A vulnerable /ping web app runs:

```
ping -c 2 [user-input]
```

Attacker sends:

```
/ping?host=;whoami
/ping?host=;ls
```

**Nginx logs show**:

```
GET /ping?host=;whoami
```

**Takeaways:**

- The attacker’s IP: `10.14.105.255`
    
- Input validation is non-existent
    
- Attack leads straight to full OS command execution
    

---

## 🌲 Process Tree Analysis (Your Best Friend)

When you see a suspicious command, **trace its parent.**

Tool: `auditd`

```
ausearch -i -x whoami
ausearch -i --pid <parent_pid>
ausearch -i --ppid <parent_pid>
```

**Goal:** Find _what launched the suspicious command._

Example findings:

```
whoami
↳ /bin/sh
↳ python3 /opt/mywebapp/app.py   ← THAT'S YOUR INITIAL ENTRY POINT
```

Once you know **what process spawned the behavior**, you know **where the breach began.**

---

## 👨‍💻 Human-Led and Supply Chain Risks

Sometimes Linux gets popped because a human was tired:

|Scenario|What Happened|Impact|
|---|---|---|
|Admin blindly runs a “fix script” from a forum|`curl|bash` strikes again|
|Developer mistypes package name|`fastpi` instead of `fastapi`|Attacker pre-seeded PyPI malware|
|Supply chain library backdoor|XZ Utils backdoor incident|Mass compromise potential|

Even _trusted_ software can betray you.  
(Sad, poetic, and very on-brand for cybersecurity.)

---

## 🧭 Core Detection Strategy (Universal)

1. **Start with an alert** (weird command, strange login, malicious IP)
    
2. **Build the process tree**
    
3. **Identify the originating user / service**
    
4. **Decide: expected behavior or compromise**
    
5. **If bad → escalate, isolate, respond**
    

This flow works for:

- SSH breaches
    
- Web app exploitation
    
- Supply chain backdoors
    
- Insider / human mistakes
    

---

## 🏁 TL;DR (The Ultra-Compressed Version)

- SSH is overexposed and often weakly secured
    
- Public apps are a massive attack surface
    
- Logs + process tree = your best weapon
    
- Always question **who**, **from where**, **when**, and **how**
    
- Humans remain the weakest link (we are predictable)
    

---

