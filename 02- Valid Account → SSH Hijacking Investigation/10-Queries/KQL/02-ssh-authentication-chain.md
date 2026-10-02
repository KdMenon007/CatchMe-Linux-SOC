# Project 02 — SSH Authentication Chain Hunt

## Purpose

Identify SSH authentication activity associated with the Project 02 attacker source and correlate the SSH daemon process with authentication events.

## KQL

```kql
host.name : "soc-linux" and process.name : "sshd" and source.ip : "192.168.1.10"
```

## Time Range

**2026-10-02 08:36:00 IST → 2026-10-02 08:37:00 IST**

## Investigation Context

The query focuses on:

* Host: `soc-linux`
* Process: `sshd`
* Source IP: `192.168.1.10`

It was used to investigate SSH activity originating from the controlled Kali attacker system.

## Observed Result

The query returned **3 events** during the selected time window.

Observed event actions included:

* `authentication_failure`
* `ssh_login`

## Evidence

Corresponding hunting screenshot:

`09-Screenshots/Hunting/02-ssh-authentication-chain-hunt.png`

Related telemetry evidence:

`09-Screenshots/Telemetry/`

## Correlation Note

The Elastic events should be correlated with the Linux SSH journal and raw authentication logs when determining whether an individual event represents successful or failed authentication.

The Project 02 Linux SSH journal provides the authoritative context for the observed authentication sequence.

## Project Mapping

* **Project:** 02 — Valid Account → SSH Hijacking
* **Phase:** Threat Hunting
* **Platform:** Elastic Security
* **Data Source:** Linux SSH / authentication telemetry
* **Endpoint:** `soc-linux`
* **Attacker:** `192.168.1.10`
* **Process:** `sshd`


