# 04 — Detection

## Detection Status

**Status:** Validated  
**Detection Platform:** Elastic Security  
**Rule Type:** Custom Query  
**Query Language:** KQL

---

## Detection Rule

### Rule Name

`CatchMe - Linux SSH Valid Account Activity`

### Detection Query

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

---

## Detection Objective

The objective of this detection is to identify SSH activity involving the monitored Linux endpoint and provide SOC visibility into remote SSH authentication and session activity.

The detection focuses on:

* SSH activity on `soc-linux`
* `sshd` process activity
* Remote source IP addresses
* User accounts involved in SSH activity
* Authentication and session telemetry

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

## Lab Environment

### Attacker

```text
Hostname: kiran
IP Address: 192.168.1.10
Role: Kali Linux attacker
```

### Target

```text
Hostname: soc-linux
IP Address: 192.168.1.16
Role: Linux monitored endpoint
```

### Account

```text
Username: socadmin
```

### Detection Platform

```text
Hostname: elastic-siem
IP Address: 192.168.1.11
Platform: Elastic Security
```

---

## Detection Validation

After the detection rule was created and enabled, a fresh SSH connection was generated from the Kali attacker system against the monitored Linux endpoint.

The new SSH activity was ingested into Elastic and matched the custom KQL detection rule.

Elastic subsequently generated a real detection alert.

This confirms that the detection rule is operational in the lab environment.

---

## Observed Alert

### Alert Summary

| Field           | Observed Value                               |
| --------------- | -------------------------------------------- |
| Rule            | `CatchMe - Linux SSH Valid Account Activity` |
| Alert Timestamp | `Oct 2, 2026 @ 10:08:40.907`                 |
| Status          | Open                                         |
| Severity        | Medium                                       |
| Risk Score      | 47                                           |
| Event Action    | `ssh_login`                                  |
| Event Category  | `authentication`, `session`                  |
| Host            | `soc-linux`                                  |
| Process         | `sshd`                                       |
| Source IP       | `192.168.1.10`                               |
| Source Port     | `58958`                                      |
| User            | `socadmin`                                   |

---

## Elastic Alert Reason

The alert reason shown by Elastic identified an authentication/session event involving:

```text
Process: sshd
Source: 192.168.1.10:58958
User: socadmin
Host: soc-linux
```

Elastic classified the resulting alert as:

```text
Severity: Medium
Risk Score: 47
```

---

## Detection Workflow

```text
Kali Attacker
192.168.1.10
      |
      | SSH activity
      v
Linux Endpoint
soc-linux
192.168.1.16
      |
      | sshd telemetry
      v
Elastic Agent
      |
      v
Elastic Security
      |
      | KQL detection
      v
CatchMe - Linux SSH Valid Account Activity
      |
      v
Medium Alert
Risk Score: 47
```

---

## Detection Evidence

The following screenshots were captured during the actual detection validation.

### 01 — Elastic Valid Account Alert

```text
09-Screenshots/Detection/01-elastic-valid-account-alert.png
```

Shows the Elastic alert detail panel including:

* Rule name
* Alert timestamp
* Severity
* Risk score
* Event action
* Event category
* Host
* Process
* Source IP
* Source port
* Username

---

### 02 — Detection Rule Overview

```text
09-Screenshots/Detection/02-detection-rule-overview.png
```

Shows the configured Elastic detection rule including:

* Rule name
* KQL query
* Rule type
* Query language
* Severity
* Risk score
* Schedule
* Look-back period
* Index patterns

---

### 03 — Detection Alert Generated

```text
09-Screenshots/Detection/03-detection-alert-generated.png
```

Shows the Elastic Alerts view confirming:

```text
Showing: 1 alert
```

and displaying:

* Alert timestamp
* Detection rule
* Severity
* Risk score
* Alert reason
* Authentication/session event categorization

---

## Detection Result

The detection rule successfully generated an alert from fresh SSH telemetry after the rule was enabled.

The observed alert contained sufficient investigation context to identify:

* Source IP
* Source port
* Target host
* Username
* SSH process
* Event action
* Event category
* Severity
* Risk score

---

## Important Investigation Note

The detection rule identifies SSH activity based on the configured fields.

The presence of an Elastic event with:

```text
event.action: ssh_login
```

should be correlated with the underlying Linux authentication logs and surrounding Elastic events before concluding the exact authentication outcome.

The alert itself is therefore treated as the **detection trigger**, while the subsequent investigation determines the complete authentication sequence.

---

## MITRE ATT&CK Relevance

Potentially relevant techniques for this project include:

### T1078 — Valid Accounts

The project investigates the use of a legitimate Linux account during remote SSH activity.

### T1021.004 — Remote Services: SSH

SSH is the remote service used to access the Linux endpoint.

### T1059.004 — Command and Scripting Interpreter: Unix Shell

This technique is relevant only where post-authentication Unix shell activity is confirmed by endpoint telemetry.

Technique assignment should remain evidence-based and limited to activity actually observed during the investigation.

---

## Detection Validation Result

```text
Rule Created:        YES
Rule Enabled:        YES
Rule Executed:       YES
Rule Execution:      Succeeded
Matching Activity:   YES
Alert Generated:     YES
Alert Count:         1
Severity:            Medium
Risk Score:          47
```

---

## SOC Investigation Handoff

The generated alert becomes the starting point for the SOC investigation.

The next investigation steps are:

1. Identify the underlying event.
2. Correlate the source IP with Linux authentication logs.
3. Validate the authentication result.
4. Establish the SSH session timeline.
5. Identify the account involved.
6. Review post-authentication process activity.
7. Check for persistence.
8. Check for privilege escalation.
9. Determine the final security impact.

---

## Conclusion

The `CatchMe - Linux SSH Valid Account Activity` detection rule was successfully created, enabled, executed, and validated against fresh SSH telemetry.

Elastic generated a real Medium-severity alert with a risk score of 47 for SSH activity involving:

```text
Source:     192.168.1.10
Target:     soc-linux
User:       socadmin
Process:    sshd
Event:      ssh_login
Severity:   Medium
Risk Score: 47
```

This completes the verified Elastic detection stage for Project 02.

The next stage is to correlate this alert with the underlying endpoint telemetry and document the investigation findings.

