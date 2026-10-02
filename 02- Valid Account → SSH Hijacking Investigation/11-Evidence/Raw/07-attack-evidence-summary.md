# Project 02 — Attack Evidence Summary

## Evidence Type

Consolidated raw evidence summary for the Project 02 valid-account SSH activity.

## Lab Participants

| Role | Host | IP Address |
|---|---|---|
| Attacker | Kali | `192.168.1.10` |
| Target | `soc-linux` | `192.168.1.16` |
| SIEM | `elastic-siem` | `192.168.1.11` |

## Affected Account

`soladmin`

## Corrected Affected Account

The Project 02 evidence identifies the affected SSH account as:

`soladmin`

## Attack Activity

The documented activity involved SSH authentication from:

`192.168.1.10`

to:

`soc-linux (192.168.1.16)`

using the `socadmin` account.

## Confirmed Successful Authentication

The Linux SSH log recorded:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```
Timestamp:

**2026-10-02 08:36:22 IST**

## Session Activity

The successful authentication was followed by:

```text
session opened for user socadmin
session closed for user socadmin
```

## Additional Authentication Activity

A failed password attempt was subsequently recorded:

```text
Failed password for socadmin from 192.168.1.10 port 40600 ssh2
```

Timestamp:

**2026-10-02 08:36:24 IST**

## Elastic Correlation

Elastic telemetry correlated the activity using:

* Host: `soc-linux`
* User: `socadmin`
* Source IP: `192.168.1.10`
* Process: `sshd`
* Dataset: `system.auth`

Observed event actions included authentication and SSH session-related events.

## Detection Evidence

The activity was also used to validate:

`CatchMe - Linux SSH Valid Account Activity`

The separate detection validation generated an alert at:

**2026-10-02 10:08:40.907 IST**

This validation event is not part of the original attack timeline.

## Security Impact Observed

The evidence confirms valid-account SSH access to the monitored Linux endpoint.

The evidence does not demonstrate:

* Privilege escalation
* Persistence
* Credential theft from the endpoint
* Data exfiltration
* Destructive activity

## Evidence Sources

### Linux Telemetry

* `09-Screenshots/Telemetry/01-linux-raw-auth-log.png`
* `09-Screenshots/Telemetry/02-linux-ssh-journal.png`

### Elastic Telemetry

* `09-Screenshots/Telemetry/03-elastic-authentication-chain.png`
* `09-Screenshots/Telemetry/04-elastic-ssh-login-event.png`
* `09-Screenshots/Telemetry/05-elastic-raw-auth-message.png`
* `09-Screenshots/Telemetry/06-elastic-authentication-failure.png`
* `09-Screenshots/Telemetry/07-elastic-ssh-session-start.png`
* `09-Screenshots/Telemetry/08-elastic-ssh-session-end.png`

### Investigation

* `09-Screenshots/Investigation/01-alert-investigation-overview.png`
* `09-Screenshots/Investigation/02-original-ssh-login-event.png`
* `09-Screenshots/Investigation/03-authentication-event-correlation.png`
* `09-Screenshots/Investigation/04-ssh-session-activity.png`
* `09-Screenshots/Investigation/05-investigation-source-user-correlation.png`

## Evidence Integrity

This summary consolidates previously collected Project 02 evidence. It does not introduce additional attack events or outcomes.

All conclusions are limited to activity demonstrated by the available Linux and Elastic telemetry.

