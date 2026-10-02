# Project 02 — Post-SSH Activity Hunt

## Purpose

Identify SSH-related activity associated with the affected user account and the SSH daemon process during the Project 02 attack window.

## KQL

```kql
host.name : "soc-linux" and user.name : "socadmin" and process.name : "sshd"
```

## Time Range

**2026-10-02 08:36:00 IST → 2026-10-02 08:37:00 IST**

## Investigation Context

The query correlates:

* Host: `soc-linux`
* User: `socadmin`
* Process: `sshd`

It was used to examine SSH activity associated with the affected account following the valid-account access scenario.

## Observed Result

The query returned **5 events** during the selected time window.

Observed event actions included:

* `authentication_failure`
* `ssh_login`
* `logged-on`
* `logged-off`

## Evidence

Corresponding hunting screenshot:

`09-Screenshots/Hunting/03-post-ssh-activity-hunt.png`

## Correlation Note

The Elastic event stream should be correlated with the Linux SSH journal and raw authentication evidence before determining the meaning of individual authentication events.

The observed `08:36:24` activity corresponds to the subsequent failed authentication activity recorded by the Linux SSH service and should not be interpreted as a separate successful SSH login.

## Project Mapping

* **Project:** 02 — Valid Account → SSH Hijacking
* **Phase:** Threat Hunting
* **Platform:** Elastic Security
* **Data Source:** Linux SSH / authentication telemetry
* **Endpoint:** `soc-linux`
* **Account:** `socadmin`
* **Process:** `sshd`

