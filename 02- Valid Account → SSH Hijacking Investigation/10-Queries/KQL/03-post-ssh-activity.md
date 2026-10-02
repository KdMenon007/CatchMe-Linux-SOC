# Project 02 — Detection Rule Query

## Purpose

Detect SSH activity on the monitored Linux endpoint when the activity originates from a remote source.

## KQL

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : *
```

## Detection Rule

**Rule Name:** `CatchMe - Linux SSH Valid Account Activity`

**Rule Type:** Query

**Severity:** Medium

**Risk Score:** 47

**Schedule:** Every 1 minute

**Look-back:** 1 minute

**Alert Suppression:** Disabled

## Detection Logic

The rule searches for SSH daemon activity on `soc-linux` where a source IP is present.

The logic provides visibility into remote SSH activity that can then be investigated for:

* Valid-account abuse
* Unexpected SSH sources
* Suspicious remote access
* SSH authentication activity
* Post-authentication activity

## Validation

The detection rule generated an alert during Project 02 validation.

Observed alert context included:

* Host: `soc-linux`
* Process: `sshd`
* Source IP: `192.168.1.10`
* User: `socadmin`
* Event action: `ssh_login`

## Evidence

Detection screenshots:

`09-Screenshots/Detection/01-elastic-valid-account-alert.png`

`09-Screenshots/Detection/02-detection-rule-overview.png`

`09-Screenshots/Detection/03-detection-alert-generated.png`

## Project Mapping

* **Project:** 02 — Valid Account → SSH Hijacking
* **Phase:** Detection Engineering
* **Platform:** Elastic Security
* **Endpoint:** `soc-linux`
* **Process:** `sshd`


