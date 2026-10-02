# Project 02 — SSH Investigation Query

## Purpose

Correlate the affected Linux endpoint, user account, and attacker source IP during the Project 02 investigation.

## KQL

```kql
event.action : "ssh_login" and host.name : "soc-linux" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

## Investigation Scope

The query focuses on the confirmed SSH login event associated with:

* Host: `soc-linux`
* User: `socadmin`
* Source IP: `192.168.1.10`
* Process: `sshd`

## Confirmed Login Event

The Project 02 telemetry recorded an SSH login event at approximately:

**2026-10-02 08:36:22 IST**

The corresponding Linux SSH message recorded:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

The associated session was opened and subsequently closed.

## Investigation Correlation

The SSH login event was correlated with:

* Linux SSH journal
* Linux authentication logs
* Elastic authentication events
* SSH session events
* Source IP
* User account
* Detection alert

## Evidence

Investigation screenshots:

`09-Screenshots/Investigation/01-alert-investigation-overview.png`

`09-Screenshots/Investigation/02-original-ssh-login-event.png`

`09-Screenshots/Investigation/03-authentication-event-correlation.png`

`09-Screenshots/Investigation/04-ssh-session-activity.png`

`09-Screenshots/Investigation/05-investigation-source-user-correlation.png`

## Investigation Note

The investigation distinguishes the confirmed successful SSH authentication from subsequent authentication-failure activity.

Elastic event actions must be correlated with the underlying Linux SSH logs before drawing conclusions about individual authentication events.

## Project Mapping

* **Project:** 02 — Valid Account → SSH Hijacking
* **Phase:** Investigation
* **Platform:** Elastic Security
* **Endpoint:** `soc-linux`
* **Account:** `socadmin`
* **Source IP:** `192.168.1.10`
* **Process:** `sshd`
