# Detection

## Project

**Project 02 — Valid Account → SSH Hijacking**

## Detection Name

`CatchMe - Linux SSH Valid Account Activity`

## Detection Objective

Detect suspicious SSH authentication activity involving a valid Linux account and provide a SOC analyst with sufficient context to investigate potential valid-account abuse.

The detection is based on the SSH telemetry validated during Project 02.

---

## Detection Type

**Elastic Security Detection Rule**

The rule should identify SSH authentication activity on the monitored Linux endpoint and support investigation of:

- Source IP
- Target account
- SSH service
- Authentication result
- Authentication failures
- Successful authentication
- SSH session activity

---

## Detection Query

The primary validation query is:

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```


This identifies SSH daemon activity with an available source IP.

---

## Successful Authentication Query

For successful SSH authentication validation:

```kql
host.name : "soc-linux" and event.action : "ssh_login" and source.ip : *
```

This identifies SSH login events that can be investigated for:

* Source IP
* Account
* Authentication time
* SSH process
* Target endpoint

---

## Authentication Failure Query

For failed authentication validation:

```kql
host.name : "soc-linux" and event.action : "authentication_failure" and source.ip : *
```

This identifies failed SSH authentication events that can be correlated with successful authentication.

---

## Project Validation Query

The controlled Project 02 activity can be isolated with:

```kql
host.name : "soc-linux" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

This query is for controlled lab validation and investigation.

It should not be used as the permanent production detection condition because it hard-codes the laboratory attacker address.

---

## Detection Logic

The detection focuses on SSH activity involving a valid account.

```text
SSH Activity
     |
     v
Source IP Identified
     |
     v
User Account Identified
     |
     v
Authentication Activity
     |
     +------------------+
     |                  |
     v                  v
Authentication       Successful
Failure              Authentication
     |                  |
     +--------+---------+
              |
              v
     Correlated SSH Activity
              |
              v
    Valid-Account Investigation
```

---

## Rule Configuration

The final Elastic rule configuration must be based on the actual rule created in Kibana.

Record the following values after implementation:

| Configuration   | Value                                                                 |
| --------------- | --------------------------------------------------------------------- |
| Rule name       | `CatchMe - Linux SSH Valid Account Activity`                          |
| Rule type       | To be recorded from Elastic                                           |
| Query           | `host.name : "soc-linux" and process.name : "sshd" and source.ip : *` |
| Severity        | To be recorded from Elastic                                           |
| Risk score      | To be recorded from Elastic                                           |
| Schedule        | To be recorded from Elastic                                           |
| Lookback        | To be recorded from Elastic                                           |
| Rule status     | To be recorded from Elastic                                           |
| Alert generated | To be verified                                                        |

No alert-generation result is recorded in this document until the rule has been tested in Elastic.

---

## Detection Scope

The monitored endpoint is:

```text
soc-linux
192.168.1.16
```

The Project 02 controlled attacker is:

```text
192.168.1.10
```

The account used during validation is:

```text
socadmin
```

The SSH service is:

```text
TCP/22
```

---

## Production Tuning

The lab attacker address should not be permanently hard-coded into the production rule.

A production implementation should instead use behavioral or environmental context such as:

```text
Unexpected SSH source
        +
Valid account
        +
Authentication activity
```

Additional contextual tuning can include:

* Approved administrator IPs
* Approved jump hosts
* Expected management systems
* Approved service accounts
* Normal administrative access periods

---

## False Positive Considerations

Legitimate SSH administration can generate:

* Successful SSH logins
* Failed passwords
* Session creation
* Session termination

Therefore, successful SSH authentication by itself does not establish malicious activity.

The alert should be investigated using the surrounding authentication and session context.

---

## Investigation Fields

When an alert is generated, the SOC analyst should capture:

```text
host.name
source.ip
source.port
user.name
event.action
process.name
@timestamp
message
```

These fields provide the primary correlation points for the Project 02 investigation.

---

## Detection Validation Procedure

After creating the rule:

1. Confirm the rule is enabled.
2. Execute the controlled Project 02 SSH activity.
3. Wait for the configured detection interval.
4. Open the Elastic Security alerts view.
5. Locate the generated alert.
6. Open the alert details.
7. Verify the source IP.
8. Verify the username.
9. Verify the target endpoint.
10. Verify the event timestamp.
11. Correlate the alert with the raw SSH logs.
12. Capture the detection screenshot.
13. Export or preserve the final rule configuration.

---

## Required Evidence

The completed detection evidence should contain:

```text
Detection Rule
      |
      +-- Rule configuration
      |
      +-- KQL
      |
      +-- Severity
      |
      +-- Risk score
      |
      +-- Schedule
      |
      +-- Lookback
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

Only values actually observed in Elastic should be added to the final detection record.

Do not manually create:

* Alert timestamps
* Risk scores
* Severity values
* Rule IDs
* Alert IDs
* Detection results

These must be copied from the actual Elastic rule and alert.

---

## Detection Outcome

The Project 02 telemetry provides sufficient data to implement an SSH valid-account detection.

The next validation step is to create the rule in Elastic and reproduce the controlled activity.

The final detection result will be documented only after the Elastic alert has been generated and verified against the endpoint evidence.


