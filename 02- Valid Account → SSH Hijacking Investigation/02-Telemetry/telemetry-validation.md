# Project 02 — Telemetry Validation

## Validation Status

**Status:** VALIDATED  
**Date:** 02 October 2026  
**Timezone:** IST (Asia/Kolkata)  
**Target:** `soc-linux`  
**Target IP:** `192.168.1.16`  
**Attacker IP:** `192.168.1.10`  
**Account:** `socadmin`

---

## Objective

Validate that the controlled credential-acquisition and SSH activity generated endpoint authentication telemetry and that the relevant events were collected by Elastic.

---

## Endpoint SSH Telemetry

The SSH service journal recorded the following activity from attacker IP `192.168.1.10`.

### Pre-authentication Connection

```text
08:36:21 IST
Received disconnect from 192.168.1.10 port 40596 [preauth]

08:36:21 IST
Disconnected from authenticating user socadmin 192.168.1.10 port 40596 [preauth]
```
This connection did not result in authentication.

### Successful Authentication

```text
08:36:22 IST
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2

08:36:22 IST
pam_unix(sshd:session): session opened for user socadmin(uid=1000)
```

This confirms that a valid password was successfully used to authenticate the `socadmin` account over SSH.

### Session Closure

```text
08:36:22 IST
pam_unix(sshd:session): session closed for user socadmin
```

The authenticated SSH session was subsequently terminated.

### Additional Failed Authentication

```text
08:36:24 IST
Failed password for socadmin from 192.168.1.10 port 40600 ssh2

08:36:25 IST
Connection closed by authenticating user socadmin 192.168.1.10 port 40600 [preauth]
```

This confirms another authentication attempt from the same attacker source after the successful authentication event.

---

## `/var/log/auth.log` Correlation

The endpoint authentication log recorded the successful SSH authentication using UTC timestamps.

```text
2026-10-02T03:06:22.295720+00:00
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2

2026-10-02T03:06:22.296579+00:00
pam_unix(sshd:session): session opened for user socadmin(uid=1000)

2026-10-02T03:06:22.480565+00:00
pam_unix(sshd:session): session closed for user socadmin

2026-10-02T03:06:24.043290+00:00
Failed password for socadmin from 192.168.1.10 port 40600 ssh2
```

The UTC timestamps correspond to the same activity observed in the IST-based SSH journal.

---

## Elastic Telemetry

Elastic Discover successfully received SSH-related telemetry from `soc-linux`.

Observed fields included:

| Field          | Observed Value           |
| -------------- | ------------------------ |
| `host.name`    | `soc-linux`              |
| `source.ip`    | `192.168.1.10`           |
| `user.name`    | `socadmin`               |
| `process.name` | `sshd`                   |
| `event.action` | `ssh_login`              |
| `event.action` | `authentication_failure` |
| `event.action` | `started-session`        |
| `event.action` | `ended-session`          |
| `event.action` | `logged-in`              |

### Primary Elastic SSH Login Event

Observed in Elastic:

```text
Timestamp: 2026-10-02 08:36:22.295 IST
event.action: ssh_login
process.name: sshd
host.name: soc-linux
source.ip: 192.168.1.10
user.name: socadmin
```

The expanded Elastic event also contained the message:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

---

## Evidence Screenshots

Attack/telemetry screenshots were captured from Elastic Discover.

Relevant evidence includes:

* SSH login telemetry
* Authentication failure telemetry
* Session-start telemetry
* Session-end telemetry
* Expanded SSH login event
* Source IP and username correlation

Screenshots are stored under:

```text
09-Screenshots/Attack/
```

---

## Important Evidence Classification

The endpoint evidence demonstrates:

1. An attacker connection originated from `192.168.1.10`.
2. The `socadmin` account was targeted.
3. A valid password was successfully accepted at `08:36:22 IST`.
4. An SSH session was opened.
5. The session was subsequently closed.
6. Another failed authentication attempt occurred afterward.
7. Elastic received corresponding SSH authentication and session telemetry.

The successful password discovery itself should be attributed to the credential-acquisition stage and its raw attack output, not inferred solely from the SSH authentication event.

---

## Telemetry Pipeline

```text
Kali Attacker
192.168.1.10
      |
      | SSH / credential attack activity
      v
soc-linux
192.168.1.16
      |
      | sshd / auth.log / system authentication telemetry
      v
Elastic Agent
      |
      v
Elastic
      |
      v
Discover
      |
      v
SOC Investigation
```

---

## Validation Conclusion

The telemetry pipeline is functioning for Project 02.

The controlled attack activity generated observable SSH authentication and session events on `soc-linux`, and those events were successfully ingested into Elastic.

The evidence supports continued progression to the **Threat Hunting phase**.

No claim of persistence, privilege escalation, credential theft beyond the controlled password-discovery stage, or host compromise is made from this telemetry alone.


