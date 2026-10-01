# Project 01 — Screenshots

## CatchMe — Linux SSH Brute Force Detection

This directory contains the screenshot evidence collected during Project 01.

The screenshots document the complete SOC workflow from attack execution through telemetry validation, threat hunting, detection engineering, alert generation, and investigation.

Screenshots are retained as **evidence of observed activity**, while the diagrams provide the visual explanation of the workflow.

---

## Screenshot Structure

```text
screenshots/
├── attack/
├── telemetry/
├── hunting/
├── detection/
└── investigation/
```
---

# 01 — Attack Evidence

## `attack/`

Screenshots in this directory document the attacker-side activity performed from Kali Linux.

### Files

```text
02-kali-hydra-detection-test.png
02-kali-hydra-detection-test-1.png
```

### Evidence

These screenshots document the controlled Hydra SSH authentication test against:

```text
Target:
soc-linux
192.168.1.16

Attacker:
Kali
192.168.1.10

Service:
SSH
TCP/22
```

The attack produced repeated authentication failures.

No successful password compromise was demonstrated.

---

# 02 — Telemetry Evidence

## `telemetry/`

This directory contains evidence showing that the Linux endpoint generated SSH security telemetry.

### Files

```text
01-elastic-ssh-authentication-events.png
01-linux-ssh-journal-attack-evidence.png
```

### Evidence Covered

The screenshots demonstrate:

* SSH authentication events
* Source IP
* Target host
* Authentication failures
* Linux SSH journal evidence
* `sshd` activity
* Endpoint-side telemetry

The endpoint evidence establishes that the attack activity was recorded locally before being investigated through Elastic.

---

# 03 — Threat Hunting Evidence

## `hunting/`

This directory documents the KQL-based threat hunting performed in Elastic.

### Files

```text
01-kql-authentication-failures.png
02-kql-sshd-source-correlation.png
05-kql-ssh-authentication-failure-results.png
```

### Hunting Objectives

The hunting process focused on identifying:

```text
Source IP
    +
Target Host
    +
User
    +
Process
    +
Event Action
    +
Timestamp
```

The primary authentication-failure investigation used:

```kql
event.action : "authentication_failure"
and source.ip : "192.168.1.10"
and host.name : "soc-linux"
```

The investigation was narrowed to the relevant attack window to isolate the detection-validation events.

---

# 04 — Detection Evidence

## `detection/`

This directory contains evidence documenting the Elastic detection-engineering process.

### Files

```text
01-ssh-brute-force-alert-list.png
01-threshold-rule-configuration.png
01-threshold-rule-schedule.png
02-rule-actions.png
02-ssh-brute-force-alert-overview.png
02-threshold-rule-type-selection.png
03-rule-created-enabled.png
03-rule-definition-summary.png
03-ssh-brute-force-alert-metadata.png
04-rule-about-settings.png
04-rule-initial-alert-state.png
04-ssh-brute-force-alert-source-ip.png
05-brute-force-alert-generated.png
05-rule-schedule-and-actions.png
```

### Detection Evidence Covered

The screenshots document:

* Detection rule creation
* Threshold rule configuration
* Rule type selection
* Detection query
* Rule definition
* Schedule
* Look-back configuration
* Rule actions
* Rule enabled state
* Initial alert state
* Alert generation
* Alert overview
* Alert metadata
* Source IP associated with the alert

### Detection Rule

```text
CatchMe - Linux SSH Brute Force Detection
```

### Detection Logic

```text
event.action = authentication_failure
        +
process.name = sshd
        +
host.name = soc-linux
        +
group by source.ip
        +
threshold >= 3
```

### Detection Result

The validated attack generated an Elastic alert with:

```text
Severity: Medium
Risk Score: 47
Threshold Count: 4
Source IP: 192.168.1.10
Status: Open
```

---

# 05 — Investigation Evidence

## `investigation/`

This directory contains evidence used to investigate and correlate the generated alert.

### Files

```text
05-linux-ssh-journal-current-attack.png
06-linux-ssh-journal-previous-attack.png
```

Additional investigation screenshots should be stored here when they are collected during the investigation workflow.

---

# Evidence Correlation

The screenshots establish the following evidence chain:

```text
Kali Attack
     ↓
SSH Authentication Attempts
     ↓
Linux SSH Journal
     ↓
Elastic Authentication Events
     ↓
KQL Threat Hunt
     ↓
Detection Rule
     ↓
Elastic Alert
     ↓
Alert Metadata
     ↓
Source IP Correlation
     ↓
Investigation
```

---

# Primary Evidence

The strongest Project 01 evidence consists of the following stages:

### Attack

```text
Kali / Hydra
192.168.1.10
```

### Endpoint

```text
soc-linux
192.168.1.16
```

### Authentication Activity

```text
event.action = authentication_failure
process.name = sshd
```

### Source

```text
192.168.1.10
```

### Target User

```text
socadmin
```

### Detection

```text
CatchMe - Linux SSH Brute Force Detection
```

### Threshold Result

```text
Count = 4
Source IP = 192.168.1.10
```

### Alert

```text
Severity = Medium
Risk Score = 47
Status = Open
```

---

# Investigation Window

The detection-validation attack was isolated using the following Elastic time range:

```text
Oct 1, 2026
08:55 → 09:00 IST
```

The isolated investigation produced four authentication-failure events:

```text
08:57:17.887
08:57:17.888
08:57:23.642
08:57:26.518
```

All four events were associated with:

```text
Source IP: 192.168.1.10
User: socadmin
Process: sshd
Host: soc-linux
Event: authentication_failure
```

---

# Timezone Correlation

The Linux endpoint uses UTC while the Elastic/Kibana investigation was viewed in IST.

```text
Linux:
UTC

Kibana:
IST (UTC+05:30)
```

Example:

```text
03:27:17 UTC
      =
08:57:17 IST
```

This timezone difference was accounted for during the investigation timeline.

---

# Evidence Interpretation

The screenshots demonstrate:

* Repeated SSH authentication failures
* A common source IP
* A common target account
* A common target host
* `sshd` involvement
* Elastic telemetry ingestion
* KQL-based investigation
* Threshold-based detection
* Successful alert generation
* Correlation between endpoint and SIEM evidence

The screenshots do **not** demonstrate a successful compromise.

---

# Evidence Integrity

Screenshots should be preserved in their original form whenever possible.

Do not:

* Edit timestamps
* Modify event fields
* Alter alert values
* Remove relevant evidence
* Fabricate missing fields
* Add information that was not visible in the original screenshot

If screenshots are sanitized for public GitHub publication, the sanitized copies should remain clearly distinguishable from original evidence.

---

# Screenshot Naming Convention

Screenshot names use numbered prefixes and descriptive names.

```text
01-
02-
03-
04-
05-
06-
```

The numbering represents the evidence sequence within the relevant category and does not necessarily represent the chronological order of the attack.

---

# Recommended Evidence Sequence

For someone reviewing Project 01 on GitHub:

```text
01. Attack
    ↓
02. Telemetry
    ↓
03. Threat Hunting
    ↓
04. Detection Engineering
    ↓
05. Alert Generation
    ↓
06. Investigation
```

This allows the reviewer to follow the same workflow used by the SOC analyst.

---

# Screenshot Inventory

```text
screenshots/
│
├── attack/
│   ├── 02-kali-hydra-detection-test.png
│   └── 02-kali-hydra-detection-test-1.png
│
├── telemetry/
│   ├── 01-elastic-ssh-authentication-events.png
│   └── 01-linux-ssh-journal-attack-evidence.png
│
├── hunting/
│   ├── 01-kql-authentication-failures.png
│   ├── 02-kql-sshd-source-correlation.png
│   └── 05-kql-ssh-authentication-failure-results.png
│
├── detection/
│   ├── 01-ssh-brute-force-alert-list.png
│   ├── 01-threshold-rule-configuration.png
│   ├── 01-threshold-rule-schedule.png
│   ├── 02-rule-actions.png
│   ├── 02-ssh-brute-force-alert-overview.png
│   ├── 02-threshold-rule-type-selection.png
│   ├── 03-rule-created-enabled.png
│   ├── 03-rule-definition-summary.png
│   ├── 03-ssh-brute-force-alert-metadata.png
│   ├── 04-rule-about-settings.png
│   ├── 04-rule-initial-alert-state.png
│   ├── 04-ssh-brute-force-alert-source-ip.png
│   ├── 05-brute-force-alert-generated.png
│   └── 05-rule-schedule-and-actions.png
│
└── investigation/
    ├── 05-linux-ssh-journal-current-attack.png
    └── 06-linux-ssh-journal-previous-attack.png
```

