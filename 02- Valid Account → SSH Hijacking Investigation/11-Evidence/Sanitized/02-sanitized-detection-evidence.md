# Project 02 — Sanitized Detection Evidence

## Purpose

Document the validated Elastic Security detection without exposing credentials, private keys, or other sensitive authentication material.

## Detection Rule

**Rule Name:** `CatchMe - Linux SSH Valid Account Activity`

- Rule type: Query
- Severity: Medium
- Risk score: 47
- Execution interval: 1 minute
- Look-back window: 1 minute
- Alert suppression: Disabled
- Maximum alerts: 100

## Detection Query

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

## Validated Alert

A fresh controlled SSH authentication activity generated a detection alert.

| Field           | Observed Value                |
| --------------- | ----------------------------- |
| Alert status    | Open                          |
| Severity        | Medium                        |
| Risk score      | 47                            |
| Host            | `soc-linux`                   |
| Process         | `sshd`                        |
| Source IP       | `192.168.1.10`                |
| Source port     | `58958`                       |
| User            | `socadmin`                    |
| Event action    | `ssh_login`                   |
| Event category  | `authentication`, `session`   |
| Alert timestamp | `2026-10-02 10:08:40.907 IST` |

## Detection Result

The detection engine successfully identified SSH activity involving the monitored Linux endpoint.

The alert provided the following investigation pivot points:

* Source IP
* Source port
* Username
* Hostname
* SSH process
* Event action
* Event category

## Timeline Separation

The detection-validation alert at:

**2026-10-02 10:08:40.907 IST**

was generated during a separate validation activity.

It is **not** part of the original attack timeline at approximately **08:36 IST**.

Keeping these activities separate prevents detection validation from being incorrectly represented as attacker activity.

## Related Screenshots

* `09-Screenshots/Detection/01-elastic-valid-account-alert.png`
* `09-Screenshots/Detection/02-detection-rule-overview.png`
* `09-Screenshots/Detection/03-detection-alert-generated.png`

## Related Documentation

* `04-detection/detection.md`
* `04-detection/detection-logic.md`
* `04-detection/detection-rule-export.md`
* `10-Queries/KQL/04-detection-rule-query.md`

## Sanitization Controls

The following sensitive material is intentionally excluded:

* Passwords
* Private SSH keys
* Credential wordlists
* Authentication secrets
* Unnecessary system credentials
* Private configuration secrets

## Evidence Integrity

This document represents the validated detection result observed in the Project 02 Elastic Security environment.

No additional alerts, attack events, or outcomes are inferred.


