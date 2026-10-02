# Project 02 — Investigation Evidence

## Evidence Type

SOC investigation evidence documenting correlation between the detection alert, SSH authentication telemetry, source IP, and affected account.

## Investigated Host

- Hostname: `soc-linux`
- IP Address: `192.168.1.16`
- Account: `socadmin`
- SSH Process: `sshd`
- Source IP: `192.168.1.10`

## Investigation Query

```kql
event.action : "ssh_login" and host.name : "soc-linux" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

## Confirmed SSH Authentication

The investigation identified a successful SSH login at approximately:

**2026-10-02 08:36:22 IST**

The corresponding Elastic event contained:

```text
event.action: ssh_login
process.name: sshd
host.name: soc-linux
source.ip: 192.168.1.10
source.port: 40598
user.name: socadmin
data_stream.dataset: system.auth
```

The raw authentication message was:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

## Session Correlation

The successful authentication was correlated with SSH session activity.

Linux authentication evidence recorded:

```text
pam_unix(sshd:session): session opened for user socadmin(uid=1000) by socadmin(uid=0)
pam_unix(sshd:session): session closed for user socadmin
```

This confirms that the authenticated account established an SSH session on `soc-linux`.

## Authentication Failure Correlation

The investigation also identified authentication failure activity involving the same source and account.

Observed source:

```text
192.168.1.10
```

Affected account:

```text
socadmin
```

A later failed authentication was recorded at approximately:

**2026-10-02 08:36:24 IST**

with source port:

```text
40600
```

Raw Linux evidence:

```text
Failed password for socadmin from 192.168.1.10 port 40600 ssh2
```

## Investigation Timeline

| Time (IST) | Evidence                                          |
| ---------- | ------------------------------------------------- |
| 08:36:21   | Pre-authentication disconnect activity            |
| 08:36:22   | Authentication failure observed                   |
| 08:36:22   | Successful password authentication for `socadmin` |
| 08:36:22   | SSH session opened                                |
| 08:36:22   | SSH session closed                                |
| 08:36:24   | Failed password attempt                           |
| 08:36:25   | Pre-authentication connection closed              |

## Investigation Conclusion

The available evidence confirms:

* `192.168.1.10` successfully authenticated to `soc-linux`
* The affected account was `socadmin`
* Password-based SSH authentication succeeded
* An SSH session was opened and subsequently closed
* Additional failed authentication activity occurred during the same investigation window
* Elastic telemetry and Linux SSH logs correlate on the source IP, account, host, and authentication activity

The available evidence does **not** demonstrate:

* Privilege escalation
* Persistence through `authorized_keys`
* Credential theft from the endpoint
* Data exfiltration
* Destructive activity

## Detection Validation Separation

The detection validation alert generated at:

**2026-10-02 10:08:40.907 IST**

is separate from the original attack activity and is not included in the attack timeline.

## Related Screenshots

* `09-Screenshots/Investigation/01-alert-investigation-overview.png`
* `09-Screenshots/Investigation/02-original-ssh-login-event.png`
* `09-Screenshots/Investigation/03-authentication-event-correlation.png`
* `09-Screenshots/Investigation/04-ssh-session-activity.png`
* `09-Screenshots/Investigation/05-investigation-source-user-correlation.png`

## Related Evidence

* `11-Evidence/Raw/01-linux-ssh-authentication-evidence.md`
* `11-Evidence/Raw/02-elastic-ssh-authentication-evidence.md`
* `11-Evidence/Raw/03-detection-alert-evidence.md`

## Evidence Integrity Note

The investigation is based on observed Linux SSH logs and Elastic telemetry collected during the Project 02 lab. Detection-validation activity is intentionally separated from the original attack timeline.

