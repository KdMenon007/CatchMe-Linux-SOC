# Attack Simulation — Valid Account → SSH Hijacking

## 1. Objective

This attack simulation demonstrates a controlled valid-account abuse scenario against the CatchMe Linux SOC lab.

The attacker used Hydra against the controlled SSH service on `soc-linux` to perform credential guessing. A valid credential for the `socadmin` account was identified during the controlled attack. The discovered credential was then used for SSH access to the Linux endpoint.

The objective was to demonstrate:

- Controlled SSH credential guessing
- Identification of a valid lab credential
- Valid Account abuse
- SSH authentication from the attacker system
- SSH session activity
- Endpoint authentication telemetry
- Elastic SIEM telemetry correlation
- SOC evidence collection

No persistence, privilege escalation, credential dumping, destructive activity, or unauthorized endpoint modification was performed.

---

## 2. Lab Environment

| Component | Hostname | IP Address | Role |
|---|---|---:|---|
| Elastic SIEM | `elastic-siem` | `192.168.1.11` | Elasticsearch / Kibana / Fleet |
| Linux Endpoint | `soc-linux` | `192.168.1.16` | SSH target |
| Kali Attacker | `kiran` | `192.168.1.10` | Attack simulation |
| Gateway | — | `192.168.1.1` | Network gateway |

All lab systems use `Asia/Kolkata (IST)` for human-readable project timestamps.

---

## 3. Attack Scenario

### Attack Chain

```text
Kali Attacker
192.168.1.10
      |
      | Hydra credential guessing
      v
SSH Service
192.168.1.16:22
      |
      | Valid credential identified
      v
socadmin
      |
      | SSH authentication
      v
soc-linux
      |
      v
SSH / Authentication Telemetry
      |
      v
Elastic Agent
      |
      v
Elastic SIEM
      |
      v
SOC Threat Hunting & Investigation
```

---

## 4. Credential Acquisition

The attacker used Hydra against the controlled SSH service on `soc-linux`.

A dedicated password wordlist containing **169 lab password candidates** was used.

The password list was stored locally on the Kali attacker system and was not committed to GitHub.

The attack was restricted to the CatchMe laboratory endpoint:

```text
Target: 192.168.1.16
Service: SSH
Account: socadmin
Source: 192.168.1.10
```

Hydra identified a valid credential for the `socadmin` account.

The actual password is intentionally excluded from the repository.

### Evidence

```text
09-Screenshots/Attack/01-hydra-credential-discovery.png
```

This screenshot provides attacker-side evidence of the controlled credential-discovery stage.

---

## 5. Valid Account SSH Access

After the credential was identified, the attacker used the `socadmin` account to authenticate to `soc-linux` through SSH.

The connection originated from:

```text
192.168.1.10
```

and targeted:

```text
192.168.1.16:22
```

The account used was:

```text
socadmin
```

After authentication, the attacker performed controlled session validation and system discovery commands:

```text
whoami
hostname
id
echo "$SSH_CONNECTION"
pwd
ls -la
uname -a
cat /etc/os-release
ps -p $$ -o pid,ppid,user,comm,args
```

The observed session confirmed:

```text
hostname: soc-linux
user: socadmin
source: 192.168.1.10
```

No persistence mechanism, SSH key modification, privilege escalation, or endpoint configuration modification was performed.

### Evidence

```text
09-Screenshots/Attack/02-ssh-valid-account-access.png
```

---

## 6. Endpoint Authentication Evidence

The Linux endpoint generated SSH authentication telemetry showing activity from the Kali attacker.

The SSH journal recorded:

```text
08:36:21 IST
Received disconnect from 192.168.1.10 port 40596 [preauth]

08:36:21 IST
Disconnected from authenticating user socadmin 192.168.1.10 port 40596 [preauth]

08:36:22 IST
pam_unix(sshd:auth): authentication failure
```

The successful credential validation was then recorded:

```text
08:36:22 IST
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2

08:36:22 IST
pam_unix(sshd:session): session opened for user socadmin(uid=1000)
```

The corresponding session was closed shortly afterward:

```text
08:36:22 IST
pam_unix(sshd:session): session closed for user socadmin
```

A subsequent authentication attempt from the same attacker source was also recorded:

```text
08:36:24 IST
Failed password for socadmin from 192.168.1.10 port 40600 ssh2

08:36:25 IST
Connection closed by authenticating user socadmin 192.168.1.10 port 40600 [preauth]
```

### Raw Linux Evidence

```text
09-Screenshots/Telemetry/01-linux-raw-auth-log.png
09-Screenshots/Telemetry/02-linux-ssh-journal.png
```

These screenshots preserve the endpoint-side authentication evidence.

---

## 7. Elastic SIEM Authentication Evidence

Elastic SIEM received SSH authentication and session telemetry from `soc-linux`.

### Authentication Correlation Query

```kql
host.name : "soc-linux" and user.name : "socadmin" and source.ip : "192.168.1.10"
```

The query returned multiple authentication and session-related events associated with the attacker source.

### Observed Authentication Sequence

| Timestamp (IST) | Event                    |
| --------------- | ------------------------ |
| 08:36:22.292    | `authentication_failure` |
| 08:36:22.294    | `was-authorized`         |
| 08:36:22.295    | `ssh_login`              |
| 08:36:22.295    | `acquired-credentials`   |
| 08:36:22.478    | `started-session`        |
| 08:36:22.479    | `acquired-credentials`   |
| 08:36:22.479    | `ended-session`          |
| 08:36:24.042    | `logged-in`              |
| 08:36:24.043    | `ssh_login`              |

The Elastic telemetry demonstrates authentication activity involving the same attacker source and `socadmin` account.

### Evidence

```text
09-Screenshots/Telemetry/03-elastic-authentication-chain.png
```

---

## 8. Expanded SSH Login Event

The successful `ssh_login` event was expanded in Elastic Discover.

Observed fields:

| Field                 | Value                       |
| --------------------- | --------------------------- |
| `event.action`        | `ssh_login`                 |
| `host.name`           | `soc-linux`                 |
| `process.name`        | `sshd`                      |
| `source.ip`           | `192.168.1.10`              |
| `user.name`           | `socadmin`                  |
| `@timestamp`          | 2026-10-02 08:36:22.295 IST |
| `agent.name`          | `soc-linux`                 |
| `data_stream.dataset` | `system.auth`               |

### Evidence

```text
09-Screenshots/Telemetry/04-elastic-ssh-login-event.png
```

---

## 9. Raw Authentication Message in Elastic

The expanded Elastic event exposed the underlying SSH authentication message:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

This correlates the structured Elastic event fields with the underlying SSH authentication message.

### Evidence

```text
09-Screenshots/Telemetry/05-elastic-raw-auth-message.png
```

---

## 10. Authentication Failure Evidence

Elastic recorded an authentication failure associated with the same attacker source and account.

Observed event:

```text
Timestamp: 2026-10-02 08:36:22.292 IST
event.action: authentication_failure
process.name: sshd
source.ip: 192.168.1.10
user.name: socadmin
host.name: soc-linux
```

### Query

```kql
host.name : "soc-linux" and process.name : "sshd" and event.action : "authentication_failure" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

### Evidence

```text
09-Screenshots/Telemetry/06-elastic-authentication-failure.png
```

---

## 11. SSH Session Establishment

Elastic recorded an SSH session-related event associated with the `socadmin` account.

Observed event:

```text
Timestamp: 2026-10-02 08:36:22.478 IST
event.action: started-session
source.ip: 192.168.1.10
user.name: socadmin
host.name: soc-linux
```

### Query

```kql
host.name : "soc-linux" and user.name : "socadmin" and source.ip : "192.168.1.10" and event.action : "started-session"
```

### Evidence

```text
09-Screenshots/Telemetry/07-elastic-ssh-session-start.png
```

---

## 12. SSH Session Termination

Elastic recorded termination of the associated session.

Observed event:

```text
Timestamp: 2026-10-02 08:36:22.479 IST
event.action: ended-session
source.ip: 192.168.1.10
user.name: socadmin
host.name: soc-linux
```

### Query

```kql
host.name : "soc-linux" and user.name : "socadmin" and source.ip : "192.168.1.10" and event.action : "ended-session"
```

### Evidence

```text
09-Screenshots/Telemetry/08-elastic-ssh-session-end.png
```

---

## 13. Post-Login Telemetry Validation

A search was performed for process telemetry associated with the authenticated `socadmin` session.

Queries included:

```kql
host.name : "soc-linux" and user.name : "socadmin" and process.name : ("bash" or "sh" or "uname" or "ls" or "whoami" or "id" or "cat")
```

and:

```kql
host.name : "soc-linux" and user.name : "socadmin" and event.category : "process"
```

No matching post-login process events were returned by the available Elastic dataset.

Therefore, the project does **not** claim that the post-login discovery commands were captured as Elastic process telemetry.

### Finding

```text
Post-login process telemetry: NOT OBSERVED
```

This is documented as a telemetry visibility limitation rather than treated as evidence that the commands were not executed.

---

## 14. Attack Timeline

| Time (IST)         | Activity                                 | Evidence Source    |
| ------------------ | ---------------------------------------- | ------------------ |
| Attack phase       | Hydra credential guessing                | Kali terminal      |
| Attack phase       | Valid `socadmin` credential identified   | Hydra output       |
| 08:36:21           | Pre-authentication connection terminated | Linux SSH journal  |
| 08:36:22.292       | Authentication failure                   | Elastic            |
| 08:36:22.295       | Successful SSH authentication            | Linux / Elastic    |
| 08:36:22.478       | SSH session-start telemetry              | Elastic            |
| 08:36:22.479       | Session-end telemetry                    | Elastic            |
| 08:36:24.042       | `logged-in` telemetry                    | Elastic            |
| 08:36:24.043       | Additional `ssh_login` telemetry         | Elastic            |
| Post-login         | Controlled discovery commands executed   | Attacker terminal  |
| Post-login         | Matching process telemetry not observed  | Elastic query      |
| Session completion | SSH activity terminated                  | Endpoint / Elastic |

---

## 15. Evidence Correlation

The attack was investigated from three perspectives.

### Attacker Perspective

```text
Hydra
↓
Valid credential identified
↓
SSH connection
↓
Authenticated socadmin access
```

Evidence:

```text
09-Screenshots/Attack/01-hydra-credential-discovery.png
09-Screenshots/Attack/02-ssh-valid-account-access.png
```

### Endpoint Perspective

```text
SSH authentication
↓
Authentication/session records
↓
Session termination
```

Evidence:

```text
09-Screenshots/Telemetry/01-linux-raw-auth-log.png
09-Screenshots/Telemetry/02-linux-ssh-journal.png
```

### SIEM Perspective

```text
authentication_failure
↓
authenticated
↓
ssh_login
↓
started-session
↓
logged-in
↓
ended-session
```

Evidence:

```text
09-Screenshots/Telemetry/03-elastic-authentication-chain.png
09-Screenshots/Telemetry/04-elastic-ssh-login-event.png
09-Screenshots/Telemetry/05-elastic-raw-auth-message.png
09-Screenshots/Telemetry/06-elastic-authentication-failure.png
09-Screenshots/Telemetry/07-elastic-ssh-session-start.png
09-Screenshots/Telemetry/08-elastic-ssh-session-end.png
```

---

## 16. Security Outcome

The simulation successfully demonstrated:

* Controlled SSH credential guessing
* Discovery of a valid lab credential
* Use of the valid `socadmin` account
* SSH authentication from `192.168.1.10`
* Authentication failure and successful authentication telemetry
* SSH session-related telemetry
* Endpoint authentication logging
* Elastic SIEM visibility and event correlation

The simulation did **not** demonstrate:

* Privilege escalation
* Persistence
* Credential dumping
* SSH authorized-key modification
* Malware execution
* Data theft
* Exfiltration
* Destructive activity
* Successful post-login process telemetry collection in Elastic

---

## 17. MITRE ATT&CK Relevance

### T1078 — Valid Accounts

The attacker used a valid credential associated with the `socadmin` account to authenticate to the Linux endpoint.

### T1021.004 — Remote Services: SSH

The attacker used SSH to remotely access `soc-linux`.

### T1059.004 — Unix Shell

Post-authentication shell activity was performed during the controlled session. However, matching process telemetry for those commands was not observed in the available Elastic dataset.

---

## 18. Evidence Handling

The actual password used during the simulation is intentionally excluded from the repository.

The local password wordlist was used only for the controlled laboratory attack and should not be committed to GitHub.

Screenshots preserve the observed attack and telemetry state without exposing the credential itself.

Raw evidence should remain separated from sanitized GitHub documentation.

---

## 19. Attack Phase Conclusion

The Project 02 attack phase demonstrated a controlled:

**Credential Guessing → Valid Account → SSH Access**

scenario.

The strongest evidence correlation is:

```text
Kali / Hydra
    ↓
Valid credential identified
    ↓
SSH authentication activity
    ↓
Successful socadmin authentication
    ↓
SSH session telemetry
    ↓
Endpoint authentication evidence
    ↓
Elastic SIEM correlation
    ↓
Session termination
```
