# 05-investigation/investigation.md

# Project 02 — Investigation

## Investigation Overview

This investigation validates and correlates SSH activity observed on the Linux endpoint `soc-linux`.

The investigation focuses on:

- Source IP: `192.168.1.10`
- Target: `soc-linux` (`192.168.1.16`)
- Account: `socadmin`
- Service: SSH
- Process: `sshd`
- Primary event type: `ssh_login`
- Supporting event types: authentication and SSH session lifecycle events

The investigation uses Elastic Security telemetry together with Linux SSH authentication logs.

---

## Investigation Objective

Determine whether the observed SSH activity represents:

1. Valid-account authentication from the Kali attacker host.
2. Successful SSH access to the Linux endpoint.
3. SSH session creation and termination.
4. Related authentication/session telemetry in Elastic.
5. Any evidence of privilege escalation or persistence.

---

## Primary Investigation Evidence

### Successful SSH Authentication

A successful SSH authentication was observed at:

- Time: `2026-10-02 08:36:22.295 IST`
- Source IP: `192.168.1.10`
- Source port: `40598`
- Destination: `soc-linux`
- User: `socadmin`
- Process: `sshd`
- Event action: `ssh_login`

The corresponding raw SSH message was:

`Accepted password for socadmin from 192.168.1.10 port 40598 ssh2`

This provides direct evidence that the valid `socadmin` account was accepted by the SSH service.

---

## Authentication Correlation

Elastic telemetry showed authentication-related events surrounding the SSH activity.

Observed event actions included:

- `authentication_failure`
- `ssh_login`
- `authenticated`
- `was-authorized`
- `acquired-credentials`
- `started-session`
- `ended-session`
- `disposed-credentials`

The events were correlated using:

- Host
- Source IP
- Username
- Process
- Timestamp
- SSH event action

The correlation confirms that the same source and account were repeatedly represented in the SSH telemetry.

---

## SSH Process Correlation

Query used:

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : "192.168.1.10"
```

The resulting telemetry showed SSH activity associated with:

* `soc-linux`
* `sshd`
* `192.168.1.10`
* `socadmin`

Observed `ssh_login` events included:

* `08:36:22.295`
* `08:36:24.043`
* `08:59:53.344`
* `10:07:29.433`

These timestamps represent observed SSH telemetry and should not all be interpreted as the same attack execution.

---

## Source and User Correlation

Query used:

```kql
host.name : "soc-linux" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

The query returned multiple authentication and session-related events.

This establishes a consistent relationship:

`192.168.1.10 → socadmin → soc-linux`

The same source IP and account were therefore useful correlation pivots during the investigation.

---

## Session Activity

The investigation identified SSH session lifecycle telemetry including:

* `started-session`
* `ended-session`
* `acquired-credentials`
* `disposed-credentials`

The observed events demonstrate that Elastic collected session-related telemetry from the SSH service.

The investigation does not interpret every lifecycle event as an independent successful login without corresponding Linux authentication evidence.

---

## Important Event Interpretation

An Elastic event at:

`2026-10-02 08:36:24.043 IST`

was represented as `ssh_login` in Elastic telemetry.

However, the corresponding Linux authentication log records:

`Failed password for socadmin from 192.168.1.10`

Therefore, this event is **not classified as a second confirmed successful SSH login**.

The investigation uses the Linux authentication log and the accepted-password message at `08:36:22.295 IST` as the authoritative evidence for the confirmed successful authentication.

---

## Detection Validation Activity

A later SSH validation was performed to verify the detection rule.

The detection alert was generated at:

`2026-10-02 10:08:40.907 IST`

The alert contained:

* Rule: `CatchMe - Linux SSH Valid Account Activity`
* Severity: Medium
* Risk score: 47
* Host: `soc-linux`
* Source IP: `192.168.1.10`
* User: `socadmin`
* Process: `sshd`
* Event action: `ssh_login`
* Source port: `58958`

This later validation event is treated separately from the original `08:36` credential-acquisition activity.

---

## Investigation Evidence

Screenshots:

```text
09-Screenshots/Investigation/
├── 01-alert-investigation-overview.png
├── 02-original-ssh-login-event.png
├── 03-authentication-event-correlation.png
├── 04-ssh-session-activity.png
└── 05-investigation-source-user-correlation.png
```

---

## Investigation Conclusion

The investigation confirmed SSH activity originating from the Kali attacker system `192.168.1.10` and targeting `soc-linux`.

A valid `socadmin` password was accepted by SSH at `08:36:22.295 IST`, providing confirmed evidence of successful valid-account authentication.

Elastic collected authentication, SSH process, credential, and session lifecycle telemetry associated with the activity.

No evidence from this investigation demonstrates:

* Privilege escalation
* SSH authorized-key persistence
* Credential dumping
* Root compromise
* Malware execution
* Data exfiltration
* Destructive activity

The investigation therefore establishes **valid-account SSH access and associated telemetry**, while avoiding unsupported conclusions about further compromise.

---

# 05-investigation/findings.md

# Project 02 — Investigation Findings

## Confirmed Findings

| Finding                                      | Evidence                              | Status           |
| -------------------------------------------- | ------------------------------------- | ---------------- |
| SSH connection originated from Kali          | Source IP `192.168.1.10`              | Confirmed        |
| Target endpoint was `soc-linux`              | `host.name = soc-linux`               | Confirmed        |
| Account used was `socadmin`                  | `user.name = socadmin`                | Confirmed        |
| SSH service processed the activity           | `process.name = sshd`                 | Confirmed        |
| Valid password was accepted                  | `Accepted password for socadmin...`   | Confirmed        |
| Successful SSH login telemetry was collected | `event.action = ssh_login`            | Confirmed        |
| SSH session telemetry was collected          | `started-session` / `ended-session`   | Confirmed        |
| Elastic detection generated an alert         | Detection alert at `10:08:40.907 IST` | Confirmed        |
| Privilege escalation occurred                | No supporting evidence                | Not demonstrated |
| Persistence was established                  | No supporting evidence                | Not demonstrated |
| Malware execution occurred                   | No supporting evidence                | Not demonstrated |
| Data exfiltration occurred                   | No supporting evidence                | Not demonstrated |

---

## Key SOC Observation

The strongest investigation correlation was:

`Source IP → User → Host → SSH Process → Authentication Event → Session Activity`

Specifically:

`192.168.1.10 → socadmin → soc-linux → sshd`

This correlation allowed the SSH activity to be investigated across multiple telemetry sources.

---

## Evidence Limitation

Elastic contains multiple SSH-related events that do not always map one-to-one with the Linux SSH authentication log.

Therefore, event names such as `ssh_login`, `logged-in`, or `authentication_failure` were not interpreted in isolation.

The confirmed successful authentication was based on the corresponding Linux SSH message:

`Accepted password for socadmin from 192.168.1.10 port 40598 ssh2`

---

# 05-investigation/timeline.md

# Project 02 — Investigation Timeline

## Attack / Investigation Timeline

| Time (IST)     | Source              | Event                           | Interpretation                                        |
| -------------- | ------------------- | ------------------------------- | ----------------------------------------------------- |
| `08:36:22.292` | Elastic             | `authentication_failure`        | Authentication-related telemetry observed             |
| `08:36:22.295` | Elastic / Linux SSH | `ssh_login` / accepted password | **Confirmed successful SSH authentication**           |
| `08:36:22.478` | Elastic             | `started-session`               | SSH session telemetry observed                        |
| `08:36:22.479` | Elastic             | `ended-session`                 | Session lifecycle telemetry observed                  |
| `08:36:24.043` | Linux SSH / Elastic | Failed password / `ssh_login`   | Not classified as a second confirmed successful login |
| `08:59:53.344` | Elastic             | `ssh_login`                     | Later SSH activity observed                           |
| `10:07:29.x`   | Elastic             | SSH session lifecycle events    | Later SSH validation activity                         |
| `10:08:40.907` | Elastic Security    | Detection alert                 | Detection rule validation alert generated             |

---

## Timeline Interpretation

The primary confirmed attack event is the successful SSH authentication at:

`2026-10-02 08:36:22.295 IST`

The later `08:59` and `10:07–10:08` events are treated as subsequent activity/validation and are not merged into the original credential-acquisition event.

This separation prevents later detection-testing activity from being incorrectly presented as part of the original attack chain.

---

## Investigation Result

The available evidence supports:

`Valid Account → SSH Authentication → SSH Session Telemetry → Detection → Investigation`

