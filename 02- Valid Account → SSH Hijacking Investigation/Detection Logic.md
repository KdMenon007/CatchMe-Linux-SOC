# Detection Logic

## Project

**Project 02 — Valid Account → SSH Hijacking**

The detection objective is to identify suspicious SSH valid-account activity involving the controlled attacker source and the `socadmin` account.

---

## Detection Objective

Detect SSH authentication activity where:

- The SSH service is involved
- Authentication occurs against `soc-linux`
- The source is an external address
- A valid Linux account is used
- Authentication failures occur around successful authentication
- SSH session activity follows authentication

The detection should provide enough context for a SOC analyst to investigate possible valid-account abuse.

---

## Detection Strategy

The detection is based on correlation of SSH authentication telemetry rather than relying only on a single successful login.

```text
SSH Authentication Failure
            |
            v
Successful SSH Authentication
            |
            v
External Source IP
            |
            v
Valid Account
            |
            v
SSH Session Activity
            |
            v
Potential Valid-Account Abuse
```

---

## Primary Detection Fields

The following fields were observed in Project 02 Elastic telemetry:

| Field          | Purpose                                 |
| -------------- | --------------------------------------- |
| `host.name`    | Identifies the Linux endpoint           |
| `source.ip`    | Identifies the SSH source               |
| `user.name`    | Identifies the account                  |
| `event.action` | Identifies authentication/session state |
| `process.name` | Identifies the SSH daemon               |
| `source.port`  | Identifies the source connection        |

---

## Detection Query — SSH Authentication

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

### Purpose

Identify SSH daemon activity originating from a source IP.

This query provides the initial event population for investigating SSH authentication activity.

---

## Detection Query — Successful SSH Login

```kql
host.name : "soc-linux" and event.action : "ssh_login" and source.ip : *
```

### Purpose

Identify SSH login events and expose:

* Source IP
* Username
* SSH process
* Target host
* Authentication timestamp

---

## Detection Query — Authentication Failure

```kql
host.name : "soc-linux" and event.action : "authentication_failure" and source.ip : *
```

### Purpose

Identify failed SSH authentication attempts that may provide context for successful valid-account authentication.

---

## Detection Query — Authentication Correlation

```kql
host.name : "soc-linux" and user.name : * and source.ip : * and process.name : "sshd"
```

### Purpose

Correlate:

```text
Source IP
+
Account
+
SSH daemon
+
Authentication activity
```

This provides the base event population for detection engineering.

---

## Detection Logic

The Project 02 detection concept is:

```text
IF

SSH activity occurs

AND

a source IP is present

AND

a Linux account is involved

AND

authentication activity is observed

AND

failed authentication and successful authentication
occur within the investigation window

THEN

generate a suspicious valid-account SSH investigation signal.
```

---

## Project 02 Detection Concept

```text
                 SSH Activity
                      |
                      v
             ┌─────────────────┐
             │   Source IP?    │
             └────────┬────────┘
                      |
                     YES
                      |
                      v
             ┌─────────────────┐
             │   User Account  │
             │    Identified   │
             └────────┬────────┘
                      |
                     YES
                      |
                      v
             ┌─────────────────┐
             │ Authentication  │
             │     Events      │
             └────────┬────────┘
                      |
             ┌────────┴─────────┐
             |                  |
             v                  v
          FAILURE            SUCCESS
             |                  |
             └────────┬─────────┘
                      |
                      v
             Correlate Activity
                      |
                      v
          Valid-Account SSH
          Investigation Signal
```

---

## Detection Scope

The detection should initially focus on the monitored Linux endpoint:

```text
host.name : "soc-linux"
```

The controlled attack source is:

```text
192.168.1.10
```

The controlled account is:

```text
socadmin
```

These values are useful for Project 02 validation but should not necessarily be hard-coded into a production detection.

---

## Lab Validation Query

For controlled Project 02 validation, the following query can isolate the attack source:

```kql
host.name : "soc-linux" and source.ip : "192.168.1.10" and user.name : "socadmin"
```

This query should be used for evidence validation and investigation.

---

## Production Detection Consideration

A production detection should avoid permanently depending on:

```text
source.ip : "192.168.1.10"
```

because that would detect only the lab attacker.

Instead, production logic should identify suspicious SSH valid-account activity based on behavioral conditions such as:

```text
SSH authentication
+
external/unusual source
+
valid account
+
authentication failures
+
successful authentication
```

The lab-specific source IP should therefore remain a validation value rather than the core production detection condition.

---

## False Positive Considerations

Legitimate administrators may:

* Authenticate through SSH
* Use valid accounts
* Generate authentication failures because of mistyped passwords
* Establish and terminate SSH sessions
* Connect from changing source IPs

Therefore, a successful SSH login alone should not automatically be treated as malicious.

Useful tuning context includes:

* Known administrative source addresses
* Known management systems
* Expected administrative accounts
* Expected SSH access windows
* Known automation
* Approved jump hosts

---

## Detection Severity Consideration

The detection severity should reflect the confidence provided by the correlation.

A single successful SSH login may represent normal administration.

A sequence involving:

```text
Multiple authentication failures
+
Successful authentication
+
Previously unusual source
+
Valid account
```

provides stronger investigation context.

The final severity should be determined during detection-rule implementation based on the actual Elastic rule configuration and the organization's risk model.

---

## Detection Validation Requirements

Before considering the detection complete, validate:

* [ ] Rule created
* [ ] Rule enabled
* [ ] Query validated against real telemetry
* [ ] Controlled attack reproduced
* [ ] Detection generated
* [ ] Alert timestamp recorded
* [ ] Source IP verified
* [ ] Username verified
* [ ] Target host verified
* [ ] Alert correlated with raw endpoint logs
* [ ] False-positive considerations documented
* [ ] Detection evidence screenshot captured
* [ ] Detection rule export preserved

---

## Evidence Requirements

The final detection evidence should contain:

```text
Detection Rule
      |
      ├── Rule configuration
      ├── KQL
      ├── Schedule
      ├── Lookback
      ├── Severity
      ├── Risk score
      └── Alert result
             |
             v
      Investigation Evidence
             |
             ├── Source IP
             ├── Account
             ├── Timestamp
             └── Related SSH events
```

No alert result should be documented until it has been generated and verified in Elastic.

---

## Detection Engineering Conclusion

Project 02 provides sufficient telemetry to build a detection around suspicious SSH valid-account activity.

The detection should correlate authentication behavior rather than treating every successful SSH login as malicious.

The next implementation step is to create and validate the **Project 02 Elastic detection rule** using the observed SSH telemetry.

