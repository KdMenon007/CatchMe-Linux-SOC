# Attack Simulation — Valid Account → SSH Hijacking

## 1. Objective

This attack simulation demonstrates a controlled valid-account abuse scenario against the CatchMe Linux SOC lab.

The attacker used a password-guessing tool against the controlled SSH service on `soc-linux`. After identifying a valid credential for the `socadmin` account, the attacker used the credential to establish an SSH session from the Kali attacker system.

The objective was to demonstrate:

- Credential guessing against SSH
- Identification of a valid account credential
- Valid Account abuse
- SSH authentication from an external source
- SSH session establishment and termination
- Endpoint and Elastic SIEM telemetry correlation
- Evidence collection from attacker, endpoint, and SIEM perspectives

No persistence, privilege escalation, credential dumping, destructive activity, or unauthorized modification of the Linux endpoint was performed.

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
socadmin authentication
      |
      | SSH login
      v
soc-linux
      |
      v
Elastic Agent / Filebeat
      |
      v
Elastic SIEM
      |
      v
SOC Investigation
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

The actual password is intentionally excluded from this documentation.

### Evidence

Attacker-side terminal evidence:

```text
09-Screenshots/Attack/01-hydra-credential-discovery.png
```

This screenshot provides the attacker-side evidence of the credential discovery stage.

---

## 5. Valid Account SSH Access

After the valid credential was identified, the attacker used the `socadmin` account to authenticate to the Linux endpoint through SSH.

The SSH connection originated from:

```text
192.168.1.10
```

and targeted:

```text
192.168.1.16
```

The account used was:

```text
socadmin
```

The attacker subsequently verified the authenticated session using controlled commands such as:

```text
whoami
hostname
id
echo "$SSH_CONNECTION"
```

No persistence mechanism or endpoint configuration modification was performed.

### Evidence

```text
09-Screenshots/Attack/02-ssh-valid-account-access.png
```

---

## 6. Endpoint Authentication Evidence

The Linux endpoint generated SSH authentication telemetry showing activity from the Kali attacker.

The raw authentication evidence contained the successful authentication message:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

The corresponding event was timestamped:

```text
2026-10-02 08:36:22.295 IST
```

### Raw Linux Evidence

```text
09-Screenshots/Attack/03-linux-raw-auth-log.png
09-Screenshots/Attack/04-linux-ssh-journal.png
```

These screenshots preserve the endpoint-side authentication evidence.

---

## 7. Elastic SIEM Authentication Evidence

Elastic SIEM was queried for SSH activity originating from the Kali attacker.

### Authentication Correlation Query

```kql
host.name : "soc-linux" and user.name : "socadmin" and source.ip : "192.168.1.10"
```

The query returned authentication and session events associated with the attack.

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

The Elastic evidence demonstrates failed authentication activity followed by successful SSH authentication and session-related events from the attacker source.

### Evidence

```text
09-Screenshots/Attack/05-elastic-authentication-chain.png
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
09-Screenshots/Attack/06-elastic-ssh-login-event.png
```

---

## 9. Raw Authentication Message in Elastic

The expanded Elastic event also exposed the underlying authentication message:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

This correlates the structured Elastic fields with the underlying SSH authentication message.

### Evidence

```text
09-Screenshots/Attack/07-elastic-raw-auth-message.png
```

---

## 10. Authentication Failure Evidence

Before the successful authentication, Elastic recorded an authentication failure from the same source and account.

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
09-Screenshots/Attack/08-elastic-authentication-failure.png
```

---

## 11. SSH Session Establishment

Elastic recorded the establishment of an SSH session associated with the `socadmin` account.

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
09-Screenshots/Attack/09-elastic-ssh-session-start.png
```

---

## 12. SSH Session Termination

Elastic also recorded termination of the associated session.

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
09-Screenshots/Attack/10-elastic-ssh-session-end.png
```

---

## 13. Post-Login Telemetry Validation

A search was performed for process telemetry associated with the authenticated `socadmin` session.

Queries included searches for:

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

This is documented as a telemetry visibility limitation rather than treated as evidence of absence of command execution.

---

## 14. Attack Timeline

| Time (IST)   | Activity                                | Evidence Source    |
| ------------ | --------------------------------------- | ------------------ |
| Attack phase | Hydra credential guessing               | Kali terminal      |
| Attack phase | Valid `socadmin` credential identified  | Hydra output       |
| 08:36:22.292 | SSH authentication failure              | Elastic            |
| 08:36:22.294 | Authorization event                     | Elastic            |
| 08:36:22.295 | SSH login                               | Elastic            |
| 08:36:22.295 | Credential-related authentication event | Elastic            |
| 08:36:22.478 | SSH session started                     | Elastic            |
| 08:36:22.479 | Session event                           | Elastic            |
| 08:36:24.042 | User logged in                          | Elastic            |
| 08:36:24.043 | SSH login event                         | Elastic            |
| Post-login   | Controlled discovery commands executed  | Attacker terminal  |
| Post-login   | Matching process telemetry not observed | Elastic query      |
| Session end  | SSH session terminated                  | Endpoint / Elastic |

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
Authenticated socadmin session
```

Evidence:

```text
01-hydra-credential-discovery.png
02-ssh-valid-account-access.png
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
03-linux-raw-auth-log.png
04-linux-ssh-journal.png
```

### SIEM Perspective

```text
authentication_failure
↓
ssh_login
↓
started-session
↓
logged-in
↓
session termination
```

Evidence:

```text
05-elastic-authentication-chain.png
06-elastic-ssh-login-event.png
07-elastic-raw-auth-message.png
08-elastic-authentication-failure.png
09-elastic-ssh-session-start.png
10-elastic-ssh-session-end.png
```

---

## 16. Security Outcome

The simulation successfully demonstrated:

* Controlled SSH credential guessing
* Discovery of a valid lab credential
* Abuse of the valid `socadmin` account
* SSH authentication from `192.168.1.10`
* Authentication failure followed by successful authentication
* SSH session establishment
* SSH session termination
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

The Project 02 attack phase successfully demonstrated a controlled:

**Credential Guessing → Valid Account → SSH Access**

scenario.

The strongest evidence correlation is:

```text
Kali / Hydra
    ↓
Valid credential identified
    ↓
SSH authentication failure
    ↓
Successful SSH login
    ↓
socadmin session established
    ↓
Endpoint authentication evidence
    ↓
Elastic SIEM correlation
    ↓
Session termination
```

The available Elastic telemetry provided strong authentication and session visibility. Post-login process telemetry was specifically searched for but was not observed, and this limitation is retained in the investigation rather than replaced with an unsupported claim.

**Attack Phase Status: COMPLETE**


