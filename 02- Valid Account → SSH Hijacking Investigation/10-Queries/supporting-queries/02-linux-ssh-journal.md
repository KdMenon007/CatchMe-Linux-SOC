# Project 02 — Linux SSH Journal Query

## Purpose

Retrieve the Linux SSH service journal to correlate Elastic telemetry with the underlying SSH authentication and session activity recorded on `soc-linux`.

## Command

```bash
sudo journalctl -u ssh --since "2026-10-02 08:36:00" --until "2026-10-02 08:37:00" --no-pager
```

## Time Range

**2026-10-02 08:36:00 IST → 2026-10-02 08:37:00 IST**

## Investigation Context

The journal query was used to validate the SSH activity associated with:

* Source IP: `192.168.1.10`
* User: `socadmin`
* Endpoint: `soc-linux`
* SSH service: `sshd`

## Relevant Observations

The Linux SSH journal recorded:

* An authentication failure
* A successful password authentication
* SSH session opening
* SSH session closing
* A subsequent failed password attempt
* Pre-authentication connection closure

The successful authentication recorded:

```text
Accepted password for socadmin from 192.168.1.10 port 40598 ssh2
```

The corresponding session was opened and subsequently closed.

A later failed authentication was recorded from source port `40600`.

## Evidence

Corresponding telemetry screenshots include:

`09-Screenshots/Telemetry/02-linux-ssh-journal.png`

`09-Screenshots/Telemetry/01-linux-raw-auth-log.png`

## Correlation

The Linux SSH journal provides endpoint-side evidence used to interpret the Elastic authentication and SSH session events.

Elastic event names should therefore be correlated with the underlying SSH journal rather than interpreted independently.

## Project Mapping

* **Project:** 02 — Valid Account → SSH Hijacking
* **Phase:** Supporting Query
* **Data Source:** Linux SSH journal
* **Endpoint:** `soc-linux`
* **Source IP:** `192.168.1.10`
* **Account:** `socadmin`


