# Project 02 — Sanitized Investigation Evidence

## Purpose

Document the SOC investigation and evidence correlation for the confirmed valid-account SSH activity without exposing credentials, private keys, or other sensitive authentication material.

## Investigation Scope

| Field | Value |
|---|---|
| Target host | `soc-linux` |
| Target IP | `192.168.1.16` |
| Account | `socadmin` |
| Source IP | `192.168.1.10` |
| Process | `sshd` |
| Protocol | SSH |
| Attack window | `2026-10-02 08:36:00–08:37:00 IST` |

## Investigation Query

```kql
event.action : "ssh_login" and host.name : "soc-linux" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

## Confirmed Authentication

The investigation identified a successful SSH authentication at approximately:

**2026-10-02 08:36:22 IST**

Observed authentication message:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

## Session Correlation

The successful authentication was correlated with:

```text
session opened for user socadmin
session closed for user socadmin
```

This confirms that the authenticated account established an SSH session on the monitored Linux endpoint.

## Authentication Failure Correlation

Additional failed authentication activity was identified from the same source IP and account.

Observed event:

```text
Failed password for socadmin from 192.168.1.10 port 40600 ssh2
```

Timestamp:

**2026-10-02 08:36:24 IST**

This event represents a failed authentication attempt and is not classified as a second successful login.

## Evidence Correlation

The investigation correlated:

1. Linux SSH authentication logs
2. Elastic authentication telemetry
3. SSH login events
4. SSH session start/end events
5. Source IP
6. Affected username
7. Detection alert context

The correlation between Linux and Elastic telemetry supports the confirmed successful SSH authentication.

## Investigation Findings

### Confirmed

* Valid account `socadmin` authenticated through SSH.
* Source IP was `192.168.1.10`.
* Target was `soc-linux`.
* Password authentication succeeded.
* An SSH session was established.
* The SSH session subsequently ended.
* Additional failed authentication activity occurred.

### Not Demonstrated

The available evidence does not demonstrate:

* Privilege escalation
* Persistence
* Credential theft from the endpoint
* Data exfiltration
* Destructive activity
* Malware execution

## Detection Validation Separation

The separate detection validation generated an alert at:

**2026-10-02 10:08:40.907 IST**

This event is excluded from the original attack timeline.

It is retained as detection-engineering validation evidence.

## Related Screenshots

* `09-Screenshots/Investigation/01-alert-investigation-overview.png`
* `09-Screenshots/Investigation/02-original-ssh-login-event.png`
* `09-Screenshots/Investigation/03-authentication-event-correlation.png`
* `09-Screenshots/Investigation/04-ssh-session-activity.png`
* `09-Screenshots/Investigation/05-investigation-source-user-correlation.png`

## Related Queries

* `10-Queries/KQL/01-valid-account-source-hunt.md`
* `10-Queries/KQL/02-ssh-authentication-chain.md`
* `10-Queries/KQL/03-post-ssh-activity.md`
* `10-Queries/KQL/05-investigation-query.md`
* `10-Queries/supporting-queries/02-linux-ssh-journal.md`
* `10-Queries/supporting-queries/03-time-correlation.md`

## Sanitization Controls

This public-facing evidence excludes:

* Passwords
* Private SSH keys
* Credential wordlists
* Authentication secrets
* Unnecessary private configuration data

## Evidence Integrity

This document contains only findings supported by the collected Linux and Elastic telemetry.

No additional attacker actions or impact are inferred beyond the observed evidence.

