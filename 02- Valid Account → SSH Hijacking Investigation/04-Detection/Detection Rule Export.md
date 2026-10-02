# Detection Rule Export — Linux SSH Valid Account Activity

## Project

**Project:** 02 — Valid Account → SSH Hijacking  
**Detection Platform:** Elastic Security  
**Rule Type:** Query  
**Query Language:** KQL

---

## Rule Identification

**Rule Name:**

`CatchMe - Linux SSH Valid Account Activity`

**Rule Type:**

`Query`

**Query Language:**

`KQL`

**Description:**

Detects SSH authentication and session activity involving the monitored Linux endpoint.

---

## Detection Query

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

---

## Rule Configuration

| Configuration             | Observed Value |
| ------------------------- | -------------- |
| Rule Type                 | Query          |
| Query Language            | KQL            |
| Severity                  | Medium         |
| Risk Score                | 47             |
| Runs Every                | 1 minute       |
| Additional Look-back Time | 1 minute       |
| Max Alerts Per Run        | 100            |
| Timeline Template         | None           |
| Alert Suppression         | Not enabled    |

---

## Index Patterns

The Elastic rule overview showed the following configured index patterns:

```text
apm-*-transaction*
auditbeat-*
endgame-*
filebeat-*
logs-*
packetbeat-*
traces-apm*
winlogbeat-*
*-elastic-cloud-logs-*
```

The detection query is evaluated against the configured Elastic Security data view/index patterns.

---

## Detection Fields

The rule uses the following primary fields:

| Field          | Purpose                           |
| -------------- | --------------------------------- |
| `host.name`    | Identifies the monitored endpoint |
| `process.name` | Identifies the SSH daemon         |
| `source.ip`    | Identifies the remote source      |

Additional fields observed in the generated alert include:

| Field            | Observed Value              |
| ---------------- | --------------------------- |
| `source.port`    | `58958`                     |
| `user.name`      | `socadmin`                  |
| `event.action`   | `ssh_login`                 |
| `event.category` | `authentication`, `session` |
| `host.name`      | `soc-linux`                 |
| `process.name`   | `sshd`                      |
| `source.ip`      | `192.168.1.10`              |

---

## Rule Execution

The rule was enabled and executed successfully.

Elastic displayed:

```text
Last response: succeeded
```

The rule was configured to execute:

```text
Every: 1 minute
Additional look-back: 1 minute
```

---

## Detection Validation

A fresh SSH activity was generated from the Kali attacker system after the rule was enabled.

### Source

```text
Hostname: kiran
IP: 192.168.1.10
```

### Target

```text
Hostname: soc-linux
IP: 192.168.1.16
```

### Account

```text
socadmin
```

The resulting SSH telemetry matched the configured KQL query.

---

## Generated Alert

Elastic Security generated one alert for the fresh activity.

### Observed Alert

| Field          | Value                                        |
| -------------- | -------------------------------------------- |
| Rule           | `CatchMe - Linux SSH Valid Account Activity` |
| Timestamp      | `Oct 2, 2026 @ 10:08:40.907`                 |
| Status         | Open                                         |
| Severity       | Medium                                       |
| Risk Score     | 47                                           |
| Event Action   | `ssh_login`                                  |
| Event Category | `authentication`, `session`                  |
| Host           | `soc-linux`                                  |
| Process        | `sshd`                                       |
| Source IP      | `192.168.1.10`                               |
| Source Port    | `58958`                                      |
| User           | `socadmin`                                   |

---

## Alert Reason

Elastic recorded the alert as an authentication/session event involving:

```text
Process: sshd
Source: 192.168.1.10:58958
User: socadmin
Host: soc-linux
```

The alert was assigned:

```text
Severity: Medium
Risk Score: 47
```

---

## Evidence Screenshots

### Rule Overview

```text
09-Screenshots/Detection/02-detection-rule-overview.png
```

### Alert Details

```text
09-Screenshots/Detection/01-elastic-valid-account-alert.png
```

### Alert Generated

```text
09-Screenshots/Detection/03-detection-alert-generated.png
```

---

## Export Status

This document records the **deployed Elastic rule configuration** observed in the Elastic Security interface.

It is a configuration record and should **not** be interpreted as a literal exported Elastic rule JSON file.

No JSON rule export is documented here because a JSON export file was not captured during this validation.

---

## Rule Reproduction

The core detection can be reproduced with:

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

Recommended configuration:

```text
Rule Type: Query
Language: KQL
Severity: Medium
Risk Score: 47
Runs Every: 1 minute
Look-back: 1 minute
Max Alerts Per Run: 100
```

---

## Validation Result

```text
Rule Created: YES
Rule Enabled: YES
Rule Executed: YES
Execution Status: Succeeded
Matching Activity: YES
Alert Generated: YES
Observed Alerts: 1
```

---

## Evidence Integrity

The configuration and alert values in this document are based on the Elastic Security interface observed during the Project 02 validation.

No unobserved alert fields or rule configuration values have been added.

The generated alert should be correlated with the underlying Linux authentication logs and Elastic events during the investigation phase.

---

## Conclusion

The `CatchMe - Linux SSH Valid Account Activity` rule was successfully configured and validated in Elastic Security.

The deployed rule used:

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```


