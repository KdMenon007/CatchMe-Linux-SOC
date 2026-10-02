# Project 02 — Linux SSH Authentication Evidence

## Evidence Type

Raw Linux SSH authentication evidence.

## Endpoint

- Hostname: `soc-linux`
- IP Address: `192.168.1.16`
- Account: `socadmin`
- SSH Service: `sshd`
- Attacker Source: `192.168.1.10`

## Attack Correlation Window

**2026-10-02 08:36:00 IST → 2026-10-02 08:37:00 IST**

Equivalent UTC:

**2026-10-02 03:06:00 UTC → 2026-10-02 03:07:00 UTC**

## Raw Linux SSH Journal Evidence

```text
Oct 02 08:36:21 soc-linux sshd[2034]: Received disconnect from 192.168.1.10 port 40596:11: Bye Bye [preauth]
Oct 02 08:36:21 soc-linux sshd[2034]: Disconnected from authenticating user socadmin 192.168.1.10 port 40596 [preauth]
Oct 02 08:36:22 soc-linux sshd[2036]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.1.10  user=socadmin
Oct 02 08:36:22 soc-linux sshd[2037]: Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
Oct 02 08:36:22 soc-linux sshd[2037]: pam_unix(sshd:session): session opened for user socadmin(uid=1000) by socadmin(uid=0)
Oct 02 08:36:22 soc-linux sshd[2037]: pam_unix(sshd:session): session closed for user socadmin
Oct 02 08:36:24 soc-linux sshd[2036]: Failed password for socadmin from 192.168.1.10 port 40600 ssh2
Oct 02 08:36:25 soc-linux sshd[2036]: Connection closed by authenticating user socadmin 192.168.1.10 port 40600 [preauth]
```

## Authentication Result

The evidence confirms a successful password-based SSH authentication:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

The successful authentication was followed by:

1. SSH session opened for `socadmin`
2. SSH session closed for `socadmin`

A later failed password attempt was also recorded:

```text
Failed password for socadmin from 192.168.1.10 port 40600 ssh2
```

## Important Interpretation

The `08:36:22` authentication failure and subsequent accepted password event occurred during the same attack activity.

The `08:36:24` `Failed password` event represents a failed authentication attempt and must **not** be interpreted as a second successful login.

## Evidence Classification

* Source: Linux `sshd` journal
* Evidence status: Raw
* Endpoint: `soc-linux`
* Attacker source: `192.168.1.10`
* Account involved: `socadmin`
* Successful authentication: Confirmed
* Privilege escalation: Not demonstrated
* Persistence: Not demonstrated
* Destructive activity: Not demonstrated
* Data exfiltration: Not demonstrated

## Related Evidence

* `09-Screenshots/Telemetry/01-linux-raw-auth-log.png`
* `09-Screenshots/Telemetry/02-linux-ssh-journal.png`
* `09-Screenshots/Telemetry/03-elastic-authentication-chain.png`
* `09-Screenshots/Telemetry/04-elastic-ssh-login-event.png`
* `09-Screenshots/Telemetry/06-elastic-authentication-failure.png`
* `09-Screenshots/Telemetry/07-elastic-ssh-session-start.png`
* `09-Screenshots/Telemetry/08-elastic-ssh-session-end.png`

## Evidence Integrity Note

This document records the observed Linux SSH authentication evidence from the Project 02 lab. No additional attack events or outcomes are inferred beyond the recorded telemetry.

