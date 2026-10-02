# Project 02 — Investigation Timeline

## Timeline Overview

This timeline documents the observed SSH authentication, session activity, and subsequent detection validation for Project 02.

All displayed times are in **IST (Asia/Kolkata)**.

---

## Primary Attack Timeline

| Time (IST) | Source | Event | Interpretation |
|---|---|---|---|
| `08:36:22.292` | Elastic | `authentication_failure` | Authentication-related telemetry observed |
| `08:36:22.295` | Elastic / Linux SSH | `ssh_login` / accepted password | **Confirmed successful SSH authentication** |
| `08:36:22.478` | Elastic | `started-session` | SSH session telemetry observed |
| `08:36:22.479` | Elastic | `ended-session` | SSH session lifecycle telemetry observed |
| `08:36:24.043` | Linux SSH / Elastic | Failed password / `ssh_login` | Not classified as a second confirmed successful login |

---

## Confirmed Successful Authentication

```text
2026-10-02 08:36:22.295 IST
```

### Authentication Details

```text
Source IP:       192.168.1.10
Source Port:     40598
Target Host:     soc-linux
Target IP:       192.168.1.16
Username:        socadmin
Process:         sshd
Event Action:    ssh_login
```

### Linux SSH Evidence

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

This event is the primary confirmed successful SSH authentication used for the investigation.

---

## Authentication and Session Sequence

```text
08:36:22.292 IST
Authentication-related telemetry
        ↓
08:36:22.295 IST
Confirmed successful SSH authentication
        ↓
08:36:22.478 IST
SSH session started
        ↓
08:36:22.479 IST
SSH session ended
        ↓
08:36:24.043 IST
Failed password / SSH-related telemetry
```

---

## Event Interpretation

The Elastic event at:

```text
08:36:24.043 IST
```

was represented as an `ssh_login` event in Elastic.

However, the corresponding Linux SSH authentication log recorded:

```text
Failed password for socadmin from 192.168.1.10
```

Therefore, this event is **not classified as a second confirmed successful SSH login**.

The successful authentication is confirmed by the Linux SSH message:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

---

## Later SSH Activity

Additional SSH activity was observed after the primary attack activity.

| Time (IST)     | Event                  | Classification            |
| -------------- | ---------------------- | ------------------------- |
| `08:59:53.344` | `ssh_login`            | Later SSH activity        |
| `10:07:29.433` | `ssh_login`            | Later validation activity |
| `10:07:29.626` | `started-session`      | Later validation activity |
| `10:07:29.627` | `acquired-credentials` | Later validation activity |
| `10:07:29.715` | `ended-session`        | Later validation activity |
| `10:07:29.716` | `disposed-credentials` | Later validation activity |
| `10:07:30.018` | `ended-session`        | Later validation activity |
| `10:07:30.018` | `disposed-credentials` | Later validation activity |
| `10:07:32.187` | `ended-session`        | Later validation activity |
| `10:07:32.187` | `disposed-credentials` | Later validation activity |

These events are kept separate from the primary `08:36` attack timeline.

---

## Detection Validation Timeline

```text
10:07:29–10:07:32 IST
Later SSH/session validation activity
        ↓
Elastic telemetry collected
        ↓
Detection rule evaluated
        ↓
10:08:40.907 IST
Detection alert generated
```

### Detection Alert

```text
Rule:        CatchMe - Linux SSH Valid Account Activity
Severity:    Medium
Risk Score:  47
Host:        soc-linux
Source IP:   192.168.1.10
User:        socadmin
Process:     sshd
Event Action: ssh_login
Source Port: 58958
```

---

## Investigation Timeline Classification

| Timeline Segment    | Classification                                            |
| ------------------- | --------------------------------------------------------- |
| `08:36:22`          | Primary attack evidence                                   |
| `08:36:24`          | Related authentication activity; not confirmed successful |
| `08:59:53`          | Later SSH activity                                        |
| `10:07:29–10:07:32` | Detection/SSH validation activity                         |
| `10:08:40.907`      | Detection alert generated                                 |

---

## Evidence Correlation

```text
192.168.1.10
     |
     v
socadmin
     |
     v
SSH / sshd
     |
     v
soc-linux
     |
     v
Authentication Telemetry
     |
     v
Session Telemetry
     |
     v
Elastic Detection
     |
     v
SOC Investigation
```

---

## Final Timeline Conclusion

The primary confirmed successful SSH authentication occurred at:

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


