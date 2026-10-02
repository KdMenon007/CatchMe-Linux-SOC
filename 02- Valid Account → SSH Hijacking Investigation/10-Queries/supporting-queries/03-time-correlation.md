# Project 02 — Time Correlation Query

## Purpose

Establish a consistent investigation time window for correlating attacker activity, Linux SSH logs, Elastic telemetry, threat hunting, detection, and investigation evidence.

## Attack Correlation Window

**2026-10-02 08:36:00 IST → 2026-10-02 08:37:00 IST**

Equivalent UTC window:

**2026-10-02 03:06:00 UTC → 2026-10-02 03:07:00 UTC**

## Elastic KQL

```kql
host.name : "soc-linux" and @timestamp >= "2026-10-02T03:06:00Z" and @timestamp <= "2026-10-02T03:07:00Z"
```

## Investigation Context

The time window was used to correlate the Project 02 SSH activity across:

* Kali attacker activity
* Linux SSH journal
* Linux authentication logs
* Elastic authentication events
* SSH session events
* Threat-hunting queries

## Key Correlation Point

The confirmed successful SSH authentication occurred at approximately:

**2026-10-02 08:36:22 IST**

The Linux SSH journal recorded:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

The corresponding Elastic telemetry recorded the SSH login and session-related activity.

## Timezone Note

The Project 02 investigation uses **IST (Asia/Kolkata)** for analyst-facing timestamps.

Elastic timestamps are stored and displayed according to the selected Kibana time configuration, while the underlying event timestamps may use UTC representation.

Therefore, timezone conversion must be considered when correlating Linux journal timestamps with Elastic events.

## Evidence

Related evidence includes:

`09-Screenshots/Telemetry/02-linux-ssh-journal.png`

`09-Screenshots/Telemetry/03-elastic-authentication-chain.png`

`09-Screenshots/Hunting/01-valid-account-source-hunt.png`

`09-Screenshots/Hunting/02-ssh-authentication-chain-hunt.png`

`09-Screenshots/Hunting/03-post-ssh-activity-hunt.png`

## Project Mapping

* **Project:** 02 — Valid Account → SSH Hijacking
* **Phase:** Supporting Query
* **Platform:** Elastic Security
* **Endpoint:** `soc-linux`
* **Attacker:** `192.168.1.10`
* **Account:** `socadmin`
* **Timezone:** Asia/Kolkata (IST)
