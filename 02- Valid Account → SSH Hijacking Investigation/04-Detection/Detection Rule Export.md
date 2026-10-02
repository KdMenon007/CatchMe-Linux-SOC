# Detection Rule Export

## Project

**Project 02 — Valid Account → SSH Hijacking**

## Rule Name

`CatchMe - Linux SSH Valid Account Activity`

## Purpose

This document records the final Elastic detection-rule configuration used for Project 02.

The rule is intended to identify SSH authentication activity that can be investigated for potential valid-account abuse.

---

## Detection Logic

The detection is based on SSH daemon activity on the monitored Linux endpoint.

### Primary KQL

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```


### Successful SSH Authentication Query

```kql
host.name : "soc-linux" and event.action : "ssh_login" and source.ip : *
```

### Authentication Failure Query

```kql
host.name : "soc-linux" and event.action : "authentication_failure" and source.ip : *
```

---

## Project Validation Query

The controlled Project 02 activity can be isolated using:

```kql
host.name : "soc-linux" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

This query is intended for controlled laboratory validation and investigation.

The attacker IP should not be hard-coded into a production detection.

---

## Rule Configuration

The following values must be recorded directly from the Elastic rule after implementation.

| Configuration   | Value                                                                 |
| --------------- | --------------------------------------------------------------------- |
| Rule name       | `CatchMe - Linux SSH Valid Account Activity`                          |
| Rule ID         | To be recorded from Elastic                                           |
| Rule type       | To be recorded from Elastic                                           |
| Query language  | KQL                                                                   |
| Query           | `host.name : "soc-linux" and process.name : "sshd" and source.ip : *` |
| Severity        | To be recorded from Elastic                                           |
| Risk score      | To be recorded from Elastic                                           |
| Schedule        | To be recorded from Elastic                                           |
| Lookback        | To be recorded from Elastic                                           |
| Rule status     | To be recorded from Elastic                                           |
| Alert ID        | To be recorded from Elastic                                           |
| Alert timestamp | To be recorded from Elastic                                           |

---

## Detection Fields

The rule relies on fields observed in the Project 02 telemetry:

```text
host.name
process.name
source.ip
user.name
event.action
source.port
@timestamp
message
```

These fields provide the primary investigation context.

---

## Detection Workflow

```text
SSH Activity
     |
     v
sshd Process
     |
     v
Source IP
     |
     v
User Account
     |
     v
Authentication Event
     |
     v
Elastic Detection Rule
     |
     v
Alert
     |
     v
SOC Investigation
```

---

## Validation

The rule must be validated using the controlled Project 02 attack.

Validation sequence:

1. Confirm the rule is enabled.
2. Execute the controlled SSH credential-testing activity.
3. Confirm authentication telemetry reaches Elastic.
4. Wait for the detection schedule.
5. Open Elastic Security Alerts.
6. Locate the Project 02 alert.
7. Open the alert details.
8. Verify the source IP.
9. Verify the username.
10. Verify the target host.
11. Verify the timestamp.
12. Correlate the alert with the endpoint SSH logs.
13. Capture the final alert screenshot.
14. Record the actual rule configuration.

---

## Alert Evidence

The final evidence should contain:

```text
Rule
 |
 +-- Rule configuration
 |
 +-- KQL
 |
 +-- Detection schedule
 |
 +-- Severity
 |
 +-- Risk score
 |
 +-- Alert
      |
      +-- Source IP
      +-- Username
      +-- Target host
      +-- Timestamp
      +-- Related SSH events
```

---

## Evidence Integrity

Rule metadata must be copied from the actual Elastic configuration.

The following values must not be invented:

* Rule ID
* Alert ID
* Alert timestamp
* Severity
* Risk score
* Schedule
* Lookback
* Alert status

If a value has not yet been verified in Elastic, it remains marked:

`To be recorded from Elastic`

---

## Production Tuning

The Project 02 attacker address:

```text
192.168.1.10
```

is a laboratory validation value.

A production implementation should not depend exclusively on this IP.

Production tuning can incorporate:

* Approved SSH administration sources
* Jump hosts
* Management systems
* Expected administrative accounts
* Expected access periods
* Known automation

---

## False Positive Considerations

Legitimate SSH administration can generate:

* Successful authentication
* Failed authentication
* Session creation
* Session termination

Therefore, an SSH authentication event should be investigated using its surrounding context.

Relevant investigation pivots include:

```text
source.ip
user.name
host.name
event.action
@timestamp
```

---

## Export Status

This document represents the detection-rule export template and validation record.

Final Elastic-generated rule metadata should be added only after the actual detection rule has been created and validated.

---

## Conclusion

Project 02 has a defined Elastic detection strategy for suspicious SSH valid-account activity.

The detection rule is designed to provide a repeatable starting point for identifying SSH activity and correlating authentication behavior.

The next step is to validate the rule in Elastic and preserve the real alert evidence.


