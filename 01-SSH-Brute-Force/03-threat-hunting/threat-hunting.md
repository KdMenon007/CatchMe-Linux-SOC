# Project 01 — Threat Hunting

## 1. Purpose

This document records the threat-hunting methodology used for **CatchMe Linux SOC — Project 01**.

The objective is to identify and validate SSH brute-force activity against the Linux endpoint before relying on the automated detection rule.

The hunting process follows:

```text
Raw Telemetry
      ↓
Broad KQL Hunt
      ↓
Narrow the Search
      ↓
Validate Event Fields
      ↓
Correlate Source / Host / User / Process
      ↓
Establish Attack Window
      ↓
Validate Against Endpoint Evidence
      ↓
Determine Finding
      ↓
Detection Engineering
```
The hunting approach follows the project methodology of starting broad, narrowing the dataset, inspecting actual events, and validating the fields before building detection logic. 

---

## 2. Hunt Scenario

### Scenario

A controlled SSH password-guessing activity was executed from the Kali attacker system against the Linux SOC endpoint.

| Attribute      | Value                            |
| -------------- | -------------------------------- |
| Attacker       | Kali                             |
| Attacker IP    | `192.168.1.10`                   |
| Target         | `soc-linux`                      |
| Target IP      | `192.168.1.16`                   |
| Target Account | `socadmin`                       |
| Service        | SSH                              |
| Port           | `22/TCP`                         |
| Process        | `sshd`                           |
| Activity       | Repeated authentication failures |
| Tool           | Hydra                            |
| Result         | `0 valid password found`         |

---

# 3. Threat Hunting Methodology

The investigation was performed in multiple stages.

## Stage 1 — Establish the Host Baseline

The first step was to identify the Linux endpoint and confirm that SSH activity should be investigated on the correct host.

```text
host.name = soc-linux
```

The investigation was restricted to:

```text
soc-linux
192.168.1.16
```

This prevents unrelated Linux or network events from contaminating the investigation.

---

# 4. Broad Hunt

The initial hunt focused on the Linux endpoint.

### KQL

```kql
host.name : "soc-linux"
```

### Objective

Identify available telemetry associated with the Linux endpoint before narrowing the investigation.

The hunting methodology recommends beginning with a broad host-level search and progressively narrowing the query based on the actual telemetry available. 

---

# 5. SSH Authentication Hunt

After identifying the relevant endpoint telemetry, the investigation was narrowed to SSH authentication activity.

### KQL

```kql
event.action : "authentication_failure" and host.name : "soc-linux"
```

### Objective

Identify authentication failures occurring on the Linux endpoint.

The resulting events were examined for:

* Source IP
* Username
* Event action
* Process
* Host
* Timestamp

---

# 6. Source IP Correlation

The investigation was then narrowed to the known controlled attack source.

### KQL

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```

### Objective

Determine whether repeated authentication failures originated from the Kali attacker system.

### Observed Fields

```text
source.ip     = 192.168.1.10
user.name     = socadmin
event.action  = authentication_failure
process.name  = sshd
host.name     = soc-linux
```

For the isolated detection-test window, the query returned:

```text
4 authentication_failure events
```

Investigation window:

```text
08:55–09:00 IST
```

This window isolates the second controlled attack and excludes the earlier test run.

---

# 7. SSH Process Correlation

The next hunt correlated authentication failures with the SSH daemon.

### KQL

```kql
process.name : "sshd" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```

### Objective

Determine whether the suspicious authentication activity was associated with the expected SSH service.

The observed process was:

```text
sshd
```

This correlation connects:

```text
Source IP
    ↓
SSH Authentication
    ↓
sshd
    ↓
soc-linux
```

---

# 8. User Correlation

The affected account was identified from the authentication events.

### Observed User

```text
socadmin
```

### Correlation

```text
source.ip = 192.168.1.10
        ↓
user.name = socadmin
        ↓
event.action = authentication_failure
        ↓
process.name = sshd
        ↓
host.name = soc-linux
```

This provides a consistent relationship between the attacker source, targeted account, authentication event, SSH process, and endpoint.

---

# 9. Time Correlation

The attack was correlated across the Kali attacker, Linux endpoint, and Elastic SIEM.

### Kali

Kali uses:

```text
Asia/Kolkata
IST (+05:30)
```

### Linux

The Linux endpoint uses:

```text
Etc/UTC
UTC (+00:00)
```

Therefore:

```text
03:27:17 UTC = 08:57:17 IST
03:27:26 UTC = 08:57:26 IST
```

### Attack Timeline

```text
08:57:17 IST
    ↓
Hydra attack begins
    ↓
SSH authentication failures
    ↓
08:57:19
Failed password attempts
    ↓
08:57:22
Additional failed passwords
    ↓
08:57:23
Elastic authentication-failure event
    ↓
08:57:25
Final failed password activity
    ↓
08:57:26
SSH connection closes
    ↓
08:57:26
Hydra reports 0 valid passwords
    ↓
08:58:02
Elastic detection alert generated
```

Timezone normalization was required because the endpoint and attacker displayed different local time zones.

---

# 10. Endpoint Correlation

Elastic findings were validated against the Linux SSH journal.

The endpoint recorded:

```text
pam_unix(sshd:auth): authentication failure
```

and:

```text
Failed password for socadmin from 192.168.1.10
```

The SSH connections subsequently closed during pre-authentication.

This provided independent endpoint evidence supporting the Elastic authentication-failure events.

---

# 11. Attack Result Validation

The Kali attack output reported:

```text
0 valid password found
```

Therefore the hunt established:

| Finding                          | Result           |
| -------------------------------- | ---------------- |
| SSH service targeted             | Confirmed        |
| Repeated authentication failures | Confirmed        |
| Source IP                        | `192.168.1.10`   |
| Target host                      | `soc-linux`      |
| Target account                   | `socadmin`       |
| SSH process                      | `sshd`           |
| Successful password              | Not observed     |
| Successful SSH login             | Not demonstrated |
| Host compromise                  | Not demonstrated |

The investigation therefore identifies **SSH password-guessing activity**, not a successful compromise.

---

# 12. Hunting Evidence

### Elastic Discover

```text
screenshots/hunting/01-kql-authentication-failures.png
screenshots/hunting/02-kql-sshd-source-correlation.png
```

### Investigation Correlation

```text
screenshots/investigation/04-source-events-correlation.png
```

### Endpoint Evidence

```text
screenshots/investigation/05-linux-ssh-journal-current-attack.png
```

### Attacker Evidence

```text
screenshots/attack/02-kali-hydra-detection-test.png
```

---

# 13. Hunt-to-Detection Transition

Threat hunting identified a repeatable behavioral pattern:

```text
event.action = authentication_failure
AND
process.name = sshd
AND
host.name = soc-linux
```

The events could then be grouped by:

```text
source.ip
```

The observed attack produced:

```text
4 matching events
```

The detection threshold was therefore designed around repeated authentication failures from the same source.

This follows the detection-engineering workflow:

```text
Observed Behavior
      ↓
Reliable Fields
      ↓
Broad Hunt
      ↓
False-Positive Reduction
      ↓
Baseline Test
      ↓
Attack Test
      ↓
Detection
      ↓
Alert
      ↓
Investigation
```

The project methodology specifically distinguishes threat hunting from detection engineering: a hunting query is not automatically a production-quality detection and must be validated against observed behavior, reliable fields, false positives, baseline activity, and attack testing. 

---

# 14. False-Positive Considerations

Potential legitimate causes of SSH authentication failures include:

* User typing an incorrect password
* Administrative login mistakes
* Automated administration scripts
* Monitoring systems
* Misconfigured SSH clients
* Scheduled jobs using outdated credentials

A single authentication failure therefore should not automatically be treated as malicious.

The Project 01 detection instead focuses on repeated failures from the same source.

---

# 15. Hunt Finding

### Finding

The threat hunt identified repeated SSH authentication failures against:

```text
socadmin
```

on:

```text
soc-linux
192.168.1.16
```

originating from:

```text
192.168.1.10
```

The activity was associated with:

```text
sshd
```

and was independently validated against the Linux SSH journal.

The controlled Hydra test reported:

```text
0 valid password found
```

Therefore:

```text
SSH password-guessing activity
        ↓
Observed
        ↓
Successful authentication
        ↓
Not observed
        ↓
Successful compromise
        ↓
Not demonstrated
```

---

# 16. Hunt Conclusion

The threat-hunting phase successfully established the behavioral indicators required for Project 01 detection engineering.

The final hunt chain was:

```text
Kali
192.168.1.10
      ↓
Repeated SSH Authentication Attempts
      ↓
socadmin
      ↓
sshd
      ↓
soc-linux
192.168.1.16
      ↓
authentication_failure
      ↓
Elastic Agent
      ↓
Elastic SIEM
      ↓
KQL Threat Hunt
      ↓
4 Matching Events
      ↓
Detection Engineering
      ↓
Threshold Alert
```

### Hunting Status

```text
[✓] Host identified
[✓] SSH telemetry identified
[✓] Authentication failures identified
[✓] Source IP correlated
[✓] User correlated
[✓] SSH process correlated
[✓] Timeline correlated
[✓] Endpoint evidence validated
[✓] Attack result validated
[✓] False-positive considerations documented
[✓] Detection candidate established
```


