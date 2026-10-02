# Project 02 — Valid Account Source Hunt

## Purpose

Identify SSH activity associated with the affected account, endpoint, and attacker source IP during the Project 02 attack window.

## KQL

```kql
host.name : "soc-linux" and user.name : "socadmin" and source.ip : "192.168.1.10"
```

## Time Range

**2026-10-02 08:36:00 IST → 2026-10-02 08:37:00 IST**

## Investigation Context

The query correlates:

* Host: `soc-linux`
* User: `socadmin`
* Source IP: `192.168.1.10`

The query was used during Project 02 threat hunting to identify activity associated with the controlled valid-account SSH scenario.

## Observed Result

The query returned **12 events** during the selected time window.

Observed event actions included authentication and SSH session-related activity.

## Evidence

Corresponding hunting screenshot:

`09-Screenshots/Hunting/01-valid-account-source-hunt.png`

## Investigation Note

Elastic events must be correlated with the Linux SSH journal and raw authentication logs before interpreting individual event actions as successful or failed authentication.

## Project Mapping

* **Project:** 02 — Valid Account → SSH Hijacking
* **Phase:** Threat Hunting
* **Platform:** Elastic Security
* **Data Source:** Linux authentication / SSH telemetry
* **Endpoint:** `soc-linux`
* **Attacker:** `192.168.1.10`
* **Account:** `socadmin`

