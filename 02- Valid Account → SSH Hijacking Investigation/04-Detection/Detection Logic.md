# Detection Logic — Linux SSH Valid Account Activity

## Project

**Project:** 02 — Valid Account → SSH Hijacking  
**Detection Platform:** Elastic Security  
**Endpoint:** `soc-linux`  
**Attacker:** `192.168.1.10`

---

## Detection Objective

The objective of this detection is to identify SSH activity involving the monitored Linux endpoint and provide visibility into remote SSH authentication and session activity.

The detection is designed to identify activity involving:

- Linux SSH
- `sshd`
- Remote source IP addresses
- User accounts
- Authentication events
- SSH session activity

The detection acts as an initial SOC trigger. The resulting alert must be correlated with the underlying Linux authentication logs and surrounding Elastic telemetry before determining the complete authentication outcome.

---

## Detection Hypothesis

An SSH connection to the monitored Linux endpoint from a remote source should generate endpoint telemetry associated with the `sshd` process.

Therefore:

> If `sshd` activity occurs on `soc-linux` and a remote source IP is present, the activity should be available for SOC detection and investigation.

---

## Detection Logic

The detection uses three primary conditions:

### 1. Target Host

```kql
host.name : "soc-linux"
```


Limits the detection to the monitored Linux endpoint.

### 2. SSH Process

```kql
process.name : "sshd"
```

Identifies activity associated with the Linux SSH daemon.

### 3. Remote Source

```kql
source.ip : *
```

Requires a source IP to be present so the SOC analyst can identify the origin of the SSH activity.

---

## Final Detection Query

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

---

## Detection Flow

```text
Remote SSH Activity
        |
        v
Source IP Identified
        |
        v
Linux Endpoint: soc-linux
        |
        v
sshd Process Telemetry
        |
        v
Elastic Agent
        |
        v
Elastic Security
        |
        v
Custom KQL Detection
        |
        v
Alert
        |
        v
SOC Investigation
```

---

## Detection Fields

| Field            | Purpose                                 |
| ---------------- | --------------------------------------- |
| `host.name`      | Identifies the monitored Linux endpoint |
| `process.name`   | Identifies the SSH daemon               |
| `source.ip`      | Identifies the remote source            |
| `source.port`    | Identifies the source connection port   |
| `user.name`      | Identifies the account involved         |
| `event.action`   | Describes the observed SSH event        |
| `event.category` | Provides event categorization           |

---

## Supporting Investigation Queries

The primary detection query identifies SSH activity. Additional queries can be used during investigation.

### SSH Login Events

```kql
host.name : "soc-linux" and event.action : "ssh_login" and source.ip : "192.168.1.10"
```

### Authentication Failures

```kql
host.name : "soc-linux" and event.action : "authentication_failure" and source.ip : "192.168.1.10"
```

### Authentication State

```kql
host.name : "soc-linux" and event.action : "authenticated"
```

### SSH Process Correlation

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : "192.168.1.10"
```

### User Correlation

```kql
host.name : "soc-linux" and user.name : "socadmin" and source.ip : "192.168.1.10"
```

---

## Detection Configuration

The rule was configured in Elastic Security as a **Custom Query** rule.

| Configuration        | Value       |
| -------------------- | ----------- |
| Rule Type            | Query       |
| Query Language       | KQL         |
| Severity             | Medium      |
| Risk Score           | 47          |
| Runs Every           | 1 minute    |
| Additional Look-back | 1 minute    |
| Max Alerts Per Run   | 100         |
| Alert Suppression    | Not enabled |

---

## Detection Validation

After the rule was enabled, fresh SSH activity was generated from the Kali attacker system:

```text
Source IP: 192.168.1.10
Target: soc-linux
Target IP: 192.168.1.16
User: socadmin
```

The resulting telemetry matched the detection query.

Elastic generated a real alert:

```text
Rule:
CatchMe - Linux SSH Valid Account Activity

Alert:
Oct 2, 2026 @ 10:08:40.907

Severity:
Medium

Risk Score:
47
```

---

## Observed Detection Event

The alert contained the following observed fields:

| Field            | Observed Value              |
| ---------------- | --------------------------- |
| `event.action`   | `ssh_login`                 |
| `event.category` | `authentication`, `session` |
| `host.name`      | `soc-linux`                 |
| `process.name`   | `sshd`                      |
| `source.ip`      | `192.168.1.10`              |
| `source.port`    | `58958`                     |
| `user.name`      | `socadmin`                  |

---

## Detection Reasoning

The detection does not independently determine whether every matching SSH event represents a successful interactive login.

Instead, it identifies relevant SSH activity and creates an investigation starting point.

The SOC analyst must correlate:

```text
Elastic Detection Alert
        +
Elastic Authentication Events
        +
Linux SSH Journal
        +
/var/log/auth.log
        +
Session / Process Telemetry
```

to establish the complete activity sequence.

This prevents the detection from incorrectly treating a single `ssh_login` telemetry event as definitive proof of successful compromise without supporting evidence.

---

## False Positives

Potential legitimate activity may include:

* Authorized administrators using SSH
* Security monitoring systems
* Automation accounts
* Configuration management
* Backup systems
* Scheduled administrative tasks
* Legitimate remote maintenance

The source IP, username, authentication result, timing, and surrounding activity should therefore be reviewed during investigation.

---

## Detection Tuning

The current rule is intentionally broad for the controlled lab.

For a production environment, additional context could be considered, including:

* Known administrative source IPs
* Approved management networks
* Service accounts
* Normal SSH login patterns
* Authentication success/failure sequences
* Unusual source locations
* Unusual login times
* Multiple accounts from one source
* Post-authentication process activity

Any tuning should be validated against the environment's normal SSH activity before deployment.

---

## MITRE ATT&CK Relevance

Potentially relevant techniques include:

### T1078 — Valid Accounts

Relevant when legitimate account credentials are used for unauthorized access.

### T1021.004 — Remote Services: SSH

SSH is the remote service used in this project.

### T1059.004 — Unix Shell

Relevant only if post-authentication Unix shell activity is confirmed through endpoint telemetry.

---

## Evidence

Detection configuration:

```text
09-Screenshots/Detection/02-detection-rule-overview.png
```

Detection alert:

```text
09-Screenshots/Detection/01-elastic-valid-account-alert.png
```

Alert list showing generated detection:

```text
09-Screenshots/Detection/03-detection-alert-generated.png
```

---

## Validation Result

```text
Detection Rule Created: YES
Detection Rule Enabled: YES
Rule Execution: Succeeded
Matching SSH Activity: YES
Alert Generated: YES
Alert Count Observed: 1
Severity: Medium
Risk Score: 47
```

---

## Conclusion

The detection logic successfully identifies SSH activity on the monitored Linux endpoint when the activity is associated with the `sshd` process and a source IP.

The logic was implemented as:

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

Fresh SSH activity matched the query and resulted in a real Elastic Security alert.

The alert is treated as the detection trigger. Authentication logs and surrounding endpoint telemetry are required to establish the complete attack sequence and determine the final security impact.


