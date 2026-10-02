# Threat Hunting

## Objective

Investigate the Project 02 SSH activity from a SOC perspective and determine whether the observed valid-account authentication from the controlled Kali attacker was consistent with the simulated attack scenario.

The hunt focuses on:

- Successful SSH authentication
- Failed authentication surrounding the successful login
- Source IP correlation
- Account correlation
- SSH session activity
- Authentication sequence
- SSH persistence checks
- Privilege escalation checks
- Post-authentication activity

---

## Hunt Scope

| Item | Value |
|---|---|
| Target | `soc-linux` |
| Target IP | `192.168.1.16` |
| Attacker | Kali |
| Attacker IP | `192.168.1.10` |
| Account | `socadmin` |
| Service | SSH |
| Port | `22` |
| Investigation Date | `02 Oct 2026` |
| Investigation Timezone | `IST` |

---

## Hunt Hypothesis

> A valid Linux account may have been abused to obtain SSH access to the monitored endpoint.

The investigation therefore correlates authentication events, source IP, account identity, SSH sessions, and available endpoint telemetry.

---

## Hunt 01 — Successful SSH Authentication

### Query

```kql
host.name : "soc-linux" and event.action : "ssh_login" and source.ip : "192.168.1.10"
```

### Observed Evidence

Elastic telemetry showed SSH login activity associated with:

```text
Source IP:
192.168.1.10

Target:
soc-linux

Account:
socadmin

Process:
sshd
```

A successful password authentication event was observed at approximately:

```text
2026-10-02 08:36:22 IST
```

### Assessment

The event confirms that the controlled attacker source successfully authenticated to the SSH service using the `socadmin` account.

---

## Hunt 02 — Authentication Failure Correlation

### Query

```kql
host.name : "soc-linux" and event.action : "authentication_failure" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

### Observed Evidence

Authentication-failure telemetry was observed from:

```text
192.168.1.10
```

against:

```text
socadmin
```

during the controlled attack window.

### Assessment

The failed authentication provides context around the credential-testing activity.

It should be correlated with the successful authentication rather than treated as an isolated event.

---

## Hunt 03 — Source IP Correlation

### Query

```kql
host.name : "soc-linux" and source.ip : "192.168.1.10"
```

### Observed Evidence

Multiple SSH-related events were associated with the controlled attacker address.

### Correlation

```text
Kali
192.168.1.10
      |
      | SSH
      v
soc-linux
192.168.1.16
      |
      | Authentication
      v
socadmin
```

### Assessment

The same controlled source IP was visible across the relevant SSH telemetry.

---

## Hunt 04 — Account Correlation

### Query

```kql
host.name : "soc-linux" and user.name : "socadmin" and source.ip : "192.168.1.10"
```

### Observed Evidence

Events associated with the `socadmin` account and attacker source IP were visible in Elastic.

### Assessment

The account and source-IP relationship provides a useful investigation pivot for identifying related SSH activity.

---

## Hunt 05 — SSH Process Correlation

### Query

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : "192.168.1.10"
```

### Observed Evidence

SSH daemon telemetry was observed for the controlled source address.

Observed SSH-related event types included:

```text
authentication_failure
ssh_login
authenticated
was-authorized
acquired-credentials
started-session
ended-session
logged-in
logged-off
syscall
```

### Assessment

The SSH daemon provides the primary process-level telemetry for correlating remote authentication activity.

---

## Hunt 06 — Authentication State

### Query

```kql
host.name : "soc-linux" and event.action : "authenticated"
```

### Observed Evidence

Elastic contained `authenticated` events associated with SSH authentication activity.

### Assessment

Authentication-state events provide an additional correlation point during valid-account investigations.

---

## Hunt 07 — SSH Session Lifecycle

### Query

```kql
host.name : "soc-linux" and source.ip : "192.168.1.10" and process.name : "sshd"
```

### Events of Interest

```text
started-session
ended-session
logged-in
logged-off
```

### Assessment

Session lifecycle telemetry can be used to correlate authentication with SSH session creation and termination.

---

## Hunt 08 — SSH Persistence Check

The Project 02 baseline showed that:

```text
/home/socadmin/.ssh/authorized_keys
```

was empty before the attack.

### Investigation

The controlled scenario did not include modification of:

```text
/home/socadmin/.ssh/authorized_keys
```

### Finding

No Project 02 evidence demonstrated SSH authorized-key persistence.

### Assessment

SSH key persistence was **not demonstrated** during this project.

---

## Hunt 09 — Privilege Escalation Check

The `socadmin` account is a member of the `sudo` group.

However:

> Membership in the `sudo` group alone does not demonstrate privilege escalation.

The controlled Project 02 scenario did not include privilege escalation.

### Finding

No Project 02 evidence demonstrated successful privilege escalation.

### Assessment

Privilege escalation was **not demonstrated**.

---

## Hunt 10 — Post-Authentication Activity

The investigation attempted to correlate SSH authentication with endpoint process activity.

### Query

```kql
host.name : "soc-linux" and source.ip : "192.168.1.10" and process.name : "sshd"
```

The available telemetry did not provide sufficient evidence to independently establish additional malicious process execution following authentication.

Therefore, no unsupported command execution or malicious process activity is claimed.

### Assessment

Additional malicious post-authentication execution was **not demonstrated by the available evidence**.

---

## Authentication Sequence

The observed activity can be represented as:

```text
Credential Testing
       |
       v
Authentication Failure
       |
       v
Valid Credential
       |
       v
Successful SSH Authentication
       |
       v
SSH Authentication / Session Telemetry
       |
       v
Session Termination
```

This sequence is consistent with the controlled Project 02 valid-account SSH simulation.

---

## Threat Hunting Findings

| Hunt                                   | Result           | Evidence                 |
| -------------------------------------- | ---------------- | ------------------------ |
| Successful SSH authentication          | Observed         | `ssh_login`              |
| Failed authentication                  | Observed         | `authentication_failure` |
| Source IP correlation                  | Confirmed        | `192.168.1.10`           |
| Account correlation                    | Confirmed        | `socadmin`               |
| SSH daemon activity                    | Observed         | `process.name : "sshd"`  |
| Authentication state                   | Observed         | `authenticated`          |
| Session lifecycle                      | Observed         | Session events           |
| SSH key persistence                    | Not demonstrated | Baseline + investigation |
| Privilege escalation                   | Not demonstrated | Scenario scope           |
| Additional malicious process execution | Not demonstrated | Available telemetry      |

---

## Key SOC Observation

The important investigation signal is the correlation of:

```text
Failed Authentication
        +
Successful Authentication
        +
External Source IP
        +
Valid Account
        +
SSH Session Activity
```

Individually, each event may have a legitimate explanation.

Together, within the controlled attack timeline, they provide useful context for investigating potential valid-account abuse.

---

## MITRE ATT&CK Relevance

### T1078 — Valid Accounts

The controlled scenario demonstrated use of a valid Linux account for remote authentication.

### T1021.004 — Remote Services: SSH

The valid account was used through SSH to access the Linux endpoint.

### T1059.004 — Unix Shell

This technique should only be mapped if sufficient telemetry confirms Unix shell execution as part of the documented attack activity.

The currently documented Elastic evidence does not independently establish additional malicious shell execution.

---

## Threat Hunting Conclusion

The Project 02 hunt confirmed correlated SSH authentication telemetry in Elastic SIEM.

The strongest observed evidence consisted of:

* Failed authentication
* Successful password authentication
* Source IP `192.168.1.10`
* Account `socadmin`
* SSH daemon telemetry
* Authentication-state telemetry
* SSH session lifecycle telemetry

No evidence currently demonstrates:

* SSH authorized-key persistence
* Privilege escalation
* Credential dumping
* Destructive activity
* Additional malicious post-authentication execution

The telemetry and hunting results provide the basis for the next phase:

**Detection Engineering.**
