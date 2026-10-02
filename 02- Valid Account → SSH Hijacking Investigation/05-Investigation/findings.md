# 05-investigation/findings.md

# Project 02 — Investigation Findings

## Confirmed Findings

| Finding | Evidence | Status |
|---|---|---|
| SSH connection originated from Kali | Source IP `192.168.1.10` | Confirmed |
| Target endpoint was `soc-linux` | `host.name = soc-linux` | Confirmed |
| Account used was `socadmin` | `user.name = socadmin` | Confirmed |
| SSH service processed the activity | `process.name = sshd` | Confirmed |
| Valid password was accepted | `Accepted password for socadmin from 192.168.1.10 port 40598 ssh2` | Confirmed |
| Successful SSH login telemetry was collected | `event.action = ssh_login` | Confirmed |
| SSH session telemetry was collected | `started-session` / `ended-session` | Confirmed |
| Elastic detection generated an alert | Detection alert at `2026-10-02 10:08:40.907 IST` | Confirmed |
| Privilege escalation occurred | No supporting evidence | Not demonstrated |
| Persistence was established | No supporting evidence | Not demonstrated |
| Malware execution occurred | No supporting evidence | Not demonstrated |
| Data exfiltration occurred | No supporting evidence | Not demonstrated |
| Destructive activity occurred | No supporting evidence | Not demonstrated |

---

## Key SOC Observation

The strongest investigation correlation was:

```text
Source IP → User → Host → SSH Process → Authentication Event → Session Activity
```

Specifically:

```text
192.168.1.10 → socadmin → soc-linux → sshd
```

This correlation allowed the SSH activity to be investigated across multiple telemetry sources.

---

## Authentication Finding

A successful password-based SSH authentication was confirmed at:

```text
2026-10-02 08:36:22.295 IST
```

Observed values:

```text
Source IP:       192.168.1.10
Source Port:     40598
Target Host:     soc-linux
Username:        socadmin
Process:         sshd
Event Action:    ssh_login
```

Linux SSH evidence:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

---

## Session Finding

Elastic telemetry showed SSH session lifecycle events associated with the source IP and account.

Observed event actions included:

```text
acquired-credentials
started-session
ended-session
disposed-credentials
```

These events demonstrate that SSH authentication/session telemetry was collected by Elastic.

---

## Important Event Interpretation

An Elastic event at:

```text
2026-10-02 08:36:24.043 IST
```

was represented as:

```text
event.action = ssh_login
```

However, the corresponding Linux SSH authentication log recorded:

```text
Failed password for socadmin from 192.168.1.10
```

Therefore, this event is **not classified as a second confirmed successful SSH login**.

The confirmed successful authentication is the `08:36:22.295 IST` event supported by the Linux `Accepted password` message.

---

## Detection Finding

A separate detection-validation SSH activity generated an Elastic Security alert at:

```text
2026-10-02 10:08:40.907 IST
```

Detection:

```text
CatchMe - Linux SSH Valid Account Activity
```

Alert attributes:

```text
Severity:       Medium
Risk Score:     47
Host:           soc-linux
Source IP:      192.168.1.10
Username:       socadmin
Process:        sshd
Event Action:   ssh_login
Source Port:    58958
```

This validation activity is kept separate from the original `08:36` attack timeline.

---

## Investigation Limitations

The collected evidence does not demonstrate:

* Privilege escalation
* Root compromise
* SSH authorized-key persistence
* Credential dumping
* Malware execution
* Data exfiltration
* Destructive activity

Therefore, these activities are not classified as confirmed findings.

---

## Evidence Screenshots

```text
09-Screenshots/Investigation/
├── 01-alert-investigation-overview.png
├── 02-original-ssh-login-event.png
├── 03-authentication-event-correlation.png
├── 04-ssh-session-activity.png
└── 05-investigation-source-user-correlation.png
```

---

## Conclusion

The investigation confirmed valid-account SSH activity from the Kali attacker system `192.168.1.10` against `soc-linux`.

The strongest confirmed finding is:

```text
Valid Account → SSH Authentication → SSH Session Telemetry → Detection → Investigation
```

No additional compromise beyond the observed SSH access was demonstrated.

# 05-investigation/timeline.md

# Project 02 — Investigation Timeline

## Timeline Overview

This timeline separates the primary credential-acquisition activity from later SSH validation and detection-testing activity.

All displayed times are in:

```text
Asia/Kolkata (IST)
```

---

## Primary Attack Timeline

| Time (IST)     | Source              | Event                           | Interpretation                                            |
| -------------- | ------------------- | ------------------------------- | --------------------------------------------------------- |
| `08:36:22.292` | Elastic             | `authentication_failure`        | Authentication-related telemetry observed                 |
| `08:36:22.295` | Elastic / Linux SSH | `ssh_login` / accepted password | **Confirmed successful SSH authentication**               |
| `08:36:22.478` | Elastic             | `started-session`               | SSH session telemetry observed                            |
| `08:36:22.479` | Elastic             | `ended-session`                 | SSH session lifecycle telemetry observed                  |
| `08:36:24.043` | Linux SSH / Elastic | Failed password / `ssh_login`   | **Not classified as a second confirmed successful login** |

---

## Confirmed Authentication Event

```text
08:36:22.295 IST
        |
        +-- Source: 192.168.1.10
        +-- Source Port: 40598
        +-- User: socadmin
        +-- Host: soc-linux
        +-- Process: sshd
        +-- Event: ssh_login
        |
        +-- Linux message:
            Accepted password for socadmin
            from 192.168.1.10 port 40598 ssh2
```

This is the primary confirmed successful authentication event used for the investigation.

---

## Session Timeline

```text
08:36:22.292
Authentication-related event
        ↓
08:36:22.295
Confirmed SSH login
        ↓
08:36:22.478
Session started
        ↓
08:36:22.479
Session ended
        ↓
08:36:24.043
Failed password / SSH-related telemetry
```

The individual Elastic events are correlated with Linux authentication evidence rather than interpreted independently.

---

## Later SSH Activity

Additional SSH activity was observed after the primary attack event:

| Time (IST)     | Event                  |
| -------------- | ---------------------- |
| `08:59:53.344` | `ssh_login`            |
| `10:07:29.433` | `ssh_login`            |
| `10:07:29.626` | `started-session`      |
| `10:07:29.627` | `acquired-credentials` |
| `10:07:29.715` | `ended-session`        |
| `10:07:29.716` | `disposed-credentials` |
| `10:07:30.018` | `ended-session`        |
| `10:07:30.018` | `disposed-credentials` |
| `10:07:32.187` | `ended-session`        |
| `10:07:32.187` | `disposed-credentials` |

These later events are treated as subsequent SSH activity and are not merged into the original `08:36` credential-acquisition timeline.

---

## Detection Validation Timeline

```text
10:07:29–10:07:32 IST
SSH/session validation activity
        ↓
Elastic telemetry collected
        ↓
Detection rule evaluated
        ↓
10:08:40.907 IST
CatchMe - Linux SSH Valid Account Activity
alert generated
```

Detection alert:

```text
Rule:        CatchMe - Linux SSH Valid Account Activity
Severity:    Medium
Risk Score:  47
Host:        soc-linux
Source IP:   192.168.1.10
User:        socadmin
Process:     sshd
Action:      ssh_login
Port:        58958
```

---

## Investigation Timeline Classification

| Timeline Segment    | Classification                                            |
| ------------------- | --------------------------------------------------------- |
| `08:36:22`          | Primary attack evidence                                   |
| `08:36:24`          | Related authentication activity; not confirmed successful |
| `08:59:53`          | Later SSH activity                                        |
| `10:07:29–10:07:32` | Later validation/investigation activity                   |
| `10:08:40.907`      | Detection validation alert                                |

---

## Timeline Conclusion

The primary confirmed event occurred at:

```text
2026-10-02 08:36:22.295 IST
```

The evidence supports:

```text
Valid Account
      ↓
SSH Authentication
      ↓
SSH Session Telemetry
      ↓
Elastic Detection
      ↓
SOC Investigation
```

Later SSH and detection-validation activity is explicitly separated from the original attack timeline to prevent contamination of the investigation evidence.


