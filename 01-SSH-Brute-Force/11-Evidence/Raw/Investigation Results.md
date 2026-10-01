# Investigation Results

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

## Evidence Type

Raw Investigation Evidence

## Investigation Source

Elastic Security / Elastic SIEM

## Investigation Window

**01 October 2026 — 08:55 IST to 09:00 IST**

## Target

| Field | Value |
|---|---|
| Target Host | `soc-linux` |
| Target IP | `192.168.1.16` |
| Target Account | `socadmin` |
| Service | SSH |
| Process | `sshd` |
| Attacker IP | `192.168.1.10` |
| Event Action | `authentication_failure` |

---

## Investigation Query

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"`
```
---

## Authentication Failure Events

The investigation returned four authentication-failure events associated with the SSH brute-force validation.

| Timestamp      | Source IP      | User       | Process | Event Action             |
| -------------- | -------------- | ---------- | ------- | ------------------------ |
| `08:57:17.887` | `192.168.1.10` | `socadmin` | `sshd`  | `authentication_failure` |
| `08:57:17.888` | `192.168.1.10` | `socadmin` | `sshd`  | `authentication_failure` |
| `08:57:23.642` | `192.168.1.10` | `socadmin` | `sshd`  | `authentication_failure` |
| `08:57:26.518` | `192.168.1.10` | `socadmin` | `sshd`  | `authentication_failure` |

---

## Event Correlation

The four events share the following characteristics:

* Same source IP: `192.168.1.10`
* Same target endpoint: `soc-linux`
* Same target account: `socadmin`
* Same service/process: `sshd`
* Same event type: `authentication_failure`
* All events occurred within the investigation window.

The repeated failures from the same source satisfied the configured detection threshold.

---

## Detection Correlation

The investigation correlated with the detection rule:

**Rule:** `CatchMe - Linux SSH Brute Force Detection`

**Rule Type:** Threshold

**Threshold:** `>= 3`

**Grouping Field:** `source.ip`

**Observed Matching Events:** `4`

**Severity:** Medium

**Risk Score:** 47

**Source IP:** `192.168.1.10`

---

## Alert Metadata

| Field                | Value                        |
| -------------------- | ---------------------------- |
| Alert Status         | Open                         |
| Original Event       | `Oct 1, 2026 @ 08:57:26.518` |
| Alert Timestamp      | `Oct 1, 2026 @ 08:58:02.247` |
| Last Detected        | `Oct 1, 2026 @ 08:58:02.254` |
| Matching Event Count | 4                            |

---

## Attack Tool Correlation

The corresponding Hydra validation produced:

```text
5 tries
0 valid password found
```

The authentication failures observed in Elastic therefore correlate with the controlled Hydra SSH brute-force simulation from `192.168.1.10`.

---

## Investigation Finding

The evidence confirms repeated SSH authentication failures against the `socadmin` account originating from `192.168.1.10`.

The activity triggered the configured Elastic detection after the threshold of three authentication failures was exceeded.

No successful SSH authentication or successful account compromise was observed during this validation.

---

## Final Investigation Status

**Classification:** Confirmed SSH brute-force activity

**Detection:** Successful

**Alert Generation:** Successful

**Source Correlation:** Successful

**Account Compromise:** Not observed

**Evidence Status:** Verified

---

