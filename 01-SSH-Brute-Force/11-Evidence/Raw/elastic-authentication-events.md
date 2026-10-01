# Elastic Authentication Events Evidence

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

---

## 1. Evidence Details

| Attribute | Value |
|---|---|
| Evidence Source | Elastic SIEM |
| Hostname | `soc-linux` |
| Endpoint IP | `192.168.1.16` |
| Source IP | `192.168.1.10` |
| Target Account | `socadmin` |
| Process | `sshd` |
| Event Action | `authentication_failure` |
| Investigation Window | `08:55–09:00 IST` |

---

## 2. Investigation Query

The authentication events were isolated using:

```kql
event.action : "authentication_failure" and source.ip : "192.168.1.10" and host.name : "soc-linux"
```
---

## 3. Observed Events

Four relevant authentication-failure events were observed during the final validation attack:

| Timestamp (IST) | Source IP      | User       | Process | Action                   |
| --------------- | -------------- | ---------- | ------- | ------------------------ |
| `08:57:17.887`  | `192.168.1.10` | `socadmin` | `sshd`  | `authentication_failure` |
| `08:57:17.888`  | `192.168.1.10` | `socadmin` | `sshd`  | `authentication_failure` |
| `08:57:23.642`  | `192.168.1.10` | `socadmin` | `sshd`  | `authentication_failure` |
| `08:57:26.518`  | `192.168.1.10` | `socadmin` | `sshd`  | `authentication_failure` |

---

## 4. Event Correlation

The common event attributes were:

```text
host.name    = soc-linux
source.ip    = 192.168.1.10
user         = socadmin
process.name = sshd
event.action = authentication_failure
```

This establishes a consistent relationship between the source, target endpoint, target account, SSH process, and authentication result.

---

## 5. Attack Timeline

```text
08:57:17 IST
      │
      ├── Hydra attack begins
      │
      ├── 08:57:17.887
      │   Authentication failure
      │
      ├── 08:57:17.888
      │   Authentication failure
      │
      ├── 08:57:23.642
      │   Authentication failure
      │
      └── 08:57:26.518
          Authentication failure
```

The event sequence falls within the same timeframe as the controlled Hydra validation.

---

## 6. Linux Endpoint Correlation

The Linux SSH journal recorded corresponding authentication activity beginning at:

```text
03:27:17 UTC
```

The equivalent IST timestamp is:

```text
03:27:17 UTC
        ↓
08:57:17 IST
```

This corresponds with the beginning of the Elastic event sequence.

---

## 7. Detection Correlation

The four authentication-failure events contributed to the threshold detection:

```text
Rule:
CatchMe - Linux SSH Brute Force Detection

Group By:
source.ip

Threshold:
>= 3
```

Observed matching events:

```text
Source IP:
192.168.1.10

Event Count:
4
```

The threshold was therefore reached during the controlled validation.

---

## 8. Alert Evidence

The resulting alert contained:

```text
Rule:
CatchMe - Linux SSH Brute Force Detection

Severity:
Medium

Risk Score:
47

Source IP:
192.168.1.10

Threshold Count:
4

Alert Status:
Open
```

Alert timing:

```text
Original Event:
Oct 1, 2026 @ 08:57:26.518

Alert Generated:
Oct 1, 2026 @ 08:58:02.247

Last Detected:
Oct 1, 2026 @ 08:58:02.254
```

---

## 9. Evidence Chain

```text
Kali
192.168.1.10
      ↓
SSH Authentication Attempts
      ↓
Linux sshd
      ↓
Elastic Agent
      ↓
authentication_failure Events
      ↓
Threshold >= 3
      ↓
Detection Alert
```

---

## 10. Investigation Result

The Elastic evidence confirms that multiple SSH authentication failures from the controlled Kali source were successfully ingested and correlated on the Linux endpoint.

The observed telemetry confirms:

```text
Authentication failures:
CONFIRMED

Source IP:
192.168.1.10

Target:
soc-linux / 192.168.1.16

Target account:
socadmin

SSH process:
sshd

Detection threshold reached:
CONFIRMED

Successful SSH authentication:
NOT OBSERVED
```

---

## 11. Evidence Status

**Elastic Authentication Events: VERIFIED**

The Elastic events provide SIEM-level evidence supporting the Project 01 SSH brute-force investigation and detection validation.
