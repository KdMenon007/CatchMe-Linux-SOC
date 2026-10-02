# Project 02 — Elastic SSH Authentication Evidence

## Evidence Type

Elastic SIEM authentication and SSH telemetry evidence.

## Endpoint

- Hostname: `soc-linux`
- IP Address: `192.168.1.16`
- Account: `socadmin`
- Attacker Source: `192.168.1.10`
- Process: `sshd`
- Dataset: `system.auth`

## Attack Correlation Window

**2026-10-02 08:36:00 IST → 2026-10-02 08:37:00 IST**

## Elastic Query

```kql
host.name : "soc-linux" and user.name : "socadmin" and source.ip : "192.168.1.10"
```

## Observed Event Sequence

Elastic telemetry recorded SSH-related activity involving:

* Host: `soc-linux`
* User: `socadmin`
* Source IP: `192.168.1.10`
* Process: `sshd`

Observed event actions included:

```text
authentication_failure
was-authorized
ssh_login
acquired-credentials
started-session
ended-session
logged-in
```

## Confirmed Successful SSH Login

The expanded successful SSH login event contained:

```text
Timestamp: 2026-10-02 08:36:22.295 IST
event.action: ssh_login
process.name: sshd
host.name: soc-linux
source.ip: 192.168.1.10
source.port: 40598
user.name: socadmin
agent.name: soc-linux
data_stream.dataset: system.auth
```

Raw event message:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

## Authentication Failure Correlation

Elastic telemetry also recorded an authentication failure at approximately:

```text
2026-10-02 08:36:22 IST
```

The corresponding source was:

```text
192.168.1.10
```

and the affected account was:

```text
socadmin
```

## Session Correlation

The Elastic event sequence included session start and session end activity for the authenticated `socadmin` SSH session.

The Linux journal independently confirms:

```text
pam_unix(sshd:session): session opened for user socadmin(uid=1000) by socadmin(uid=0)
pam_unix(sshd:session): session closed for user socadmin
```

## Important Interpretation

The Elastic event stream contains several authentication-related event actions.

The `08:36:22` `ssh_login` event is correlated with the Linux SSH message:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

The later `08:36:24` activity corresponds to the Linux:

```text
Failed password for socadmin from 192.168.1.10 port 40600 ssh2
```

It must not be interpreted as a second successful SSH authentication.

## Evidence Classification

* Source: Elastic Security / `system.auth`
* Evidence status: Raw telemetry correlation
* Successful SSH authentication: Confirmed
* Session activity: Confirmed
* Authentication failure: Confirmed
* Privilege escalation: Not demonstrated
* Persistence: Not demonstrated
* Data exfiltration: Not demonstrated
* Destructive activity: Not demonstrated

## Related Screenshots

* `09-Screenshots/Telemetry/03-elastic-authentication-chain.png`
* `09-Screenshots/Telemetry/04-elastic-ssh-login-event.png`
* `09-Screenshots/Telemetry/05-elastic-raw-auth-message.png`
* `09-Screenshots/Telemetry/06-elastic-authentication-failure.png`
* `09-Screenshots/Telemetry/07-elastic-ssh-session-start.png`
* `09-Screenshots/Telemetry/08-elastic-ssh-session-end.png`

## Related Queries

* `10-Queries/KQL/01-valid-account-source-hunt.md`
* `10-Queries/KQL/02-ssh-authentication-chain.md`
* `10-Queries/KQL/03-post-ssh-activity.md`
* `10-Queries/KQL/05-investigation-query.md`

## Evidence Integrity Note

This document records Elastic telemetry observed during Project 02. Event actions are interpreted together with the corresponding Linux SSH journal evidence to avoid incorrectly classifying individual authentication events.
