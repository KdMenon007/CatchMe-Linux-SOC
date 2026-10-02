# Project 02 — Sanitized Attack Timeline

## Purpose

Provide a GitHub-safe timeline of the confirmed Project 02 activity without exposing credentials, private keys, or other sensitive authentication material.

## Lab Context

| Role | Host | IP Address |
|---|---|---|
| Attacker | Kali | `192.168.1.10` |
| Target | `soc-linux` | `192.168.1.16` |
| SIEM | `elastic-siem` | `192.168.1.11` |

## Affected Account

`socadmin`

## Confirmed Attack Timeline

| Time (IST) | Activity | Evidence |
|---|---|---|
| 08:36:21 | Pre-authentication disconnect activity from `192.168.1.10` | Linux SSH journal |
| 08:36:22 | Authentication failure involving `socadmin` | Linux SSH / Elastic |
| 08:36:22 | Successful password authentication for `socadmin` | Linux SSH / Elastic |
| 08:36:22 | SSH session opened | Linux SSH / Elastic |
| 08:36:22 | SSH session closed | Linux SSH / Elastic |
| 08:36:24 | Failed password attempt | Linux SSH / Elastic |
| 08:36:25 | Pre-authentication connection closed | Linux SSH journal |

## Confirmed Successful Authentication

Observed Linux SSH event:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

This confirms successful password-based SSH authentication from the controlled Kali laboratory host to `soc-linux`.

## Session Activity

The successful authentication was followed by:

```text
session opened for user socadmin
session closed for user socadmin
```

## Additional Failed Authentication

A later failed authentication attempt was recorded:

```text
Failed password for socadmin from 192.168.1.10 port 40600 ssh2
```

This event is classified as a failed authentication attempt and is not treated as a second successful login.

## Detection Validation

A separate detection-engineering validation was performed after the original attack investigation.

Alert:

`CatchMe - Linux SSH Valid Account Activity`

Validation alert timestamp:

**2026-10-02 10:08:40.907 IST**

This validation activity is intentionally excluded from the original attack timeline.

## Security Impact Demonstrated

The evidence demonstrates:

* Valid-account SSH access
* Successful password authentication
* SSH session establishment
* SSH session termination
* Additional failed authentication activity

The evidence does not demonstrate:

* Privilege escalation
* Persistence
* Credential theft from the endpoint
* Data exfiltration
* Destructive activity

## Sensitive Information Handling

This sanitized document intentionally excludes:

* Passwords
* Private SSH keys
* Credential wordlists
* Authentication secrets
* Unnecessary host configuration details
* Any sensitive material unsuitable for public GitHub publication

## Related Evidence

### Raw Evidence

* `11-Evidence/Raw/01-linux-ssh-authentication-evidence.md`
* `11-Evidence/Raw/02-elastic-ssh-authentication-evidence.md`
* `11-Evidence/Raw/03-detection-alert-evidence.md`
* `11-Evidence/Raw/04-investigation-evidence.md`
* `11-Evidence/Raw/05-containment-remediation-evidence.md`
* `11-Evidence/Raw/06-final-remediation-state.md`
* `11-Evidence/Raw/07-attack-evidence-summary.md`

### Screenshots

* `09-Screenshots/Attack/`
* `09-Screenshots/Telemetry/`
* `09-Screenshots/Hunting/`
* `09-Screenshots/Detection/`
* `09-Screenshots/Investigation/`
* `09-Screenshots/Incident-Response/`

## Publication Safety

This document is intended as a sanitized portfolio artifact. No credentials or private authentication material should be committed to the GitHub repository.


