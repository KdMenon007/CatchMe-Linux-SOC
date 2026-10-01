# Detection Alert Evidence

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

---

## 1. Evidence Details

| Attribute | Value |
|---|---|
| Evidence Source | Elastic Security Detection Alert |
| Detection Rule | `CatchMe - Linux SSH Brute Force Detection` |
| Rule Type | Threshold |
| Source IP | `192.168.1.10` |
| Target Host | `soc-linux` |
| Target IP | `192.168.1.16` |
| Target Account | `socadmin` |
| Severity | Medium |
| Risk Score | 47 |
| Threshold | `>= 3` |
| Matching Events | 4 |
| Alert Status | Open |

---

## 2. Detection Logic

The detection rule was configured to identify repeated SSH authentication failures.

```text
event.action : "authentication_failure"
and
process.name : "sshd"
and
host.name : "soc-linux"`
```
The rule groups matching events by:

```text
source.ip
```

and triggers when:

```text
3 or more matching events
```

are observed within the configured detection window.

---

## 3. Detection Configuration

```text
Rule Name:
CatchMe - Linux SSH Brute Force Detection

Rule Type:
Threshold

Group By:
source.ip

Threshold:
>= 3

Severity:
Medium

Risk Score:
47

Schedule:
1 minute

Lookback:
1 minute

Suppression:
OFF
```

---

## 4. Detection Trigger

During the controlled validation attack, Elastic identified:

```text
Source IP:
192.168.1.10

Matching Events:
4
```

The configured threshold was:

```text
>= 3
```

Therefore, the detection condition was reached.

---

## 5. Matching Events

The four observed authentication-failure events were:

```text
08:57:17.887 IST
08:57:17.888 IST
08:57:23.642 IST
08:57:26.518 IST
```

Common attributes:

```text
host.name    = soc-linux
source.ip    = 192.168.1.10
user         = socadmin
process.name = sshd
event.action = authentication_failure
```

---

## 6. Alert Timeline

```text
08:57:17.887
    │
    ├── Authentication failure
    │
08:57:17.888
    │
    ├── Authentication failure
    │
08:57:23.642
    │
    ├── Authentication failure
    │
08:57:26.518
    │
    └── Authentication failure
            │
            ▼
      Threshold reached
            │
            ▼
08:58:02.247
      Alert generated
```

---

## 7. Alert Metadata

The generated alert contained:

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

Status:
Open
```

Timing metadata:

```text
Original Event:
Oct 1, 2026 @ 08:57:26.518

Alert Timestamp:
Oct 1, 2026 @ 08:58:02.247

Last Detected:
Oct 1, 2026 @ 08:58:02.254
```

---

## 8. Detection Evidence Flow

```text
Kali Attacker
192.168.1.10
      │
      │ SSH authentication attempts
      ▼
Linux Endpoint
192.168.1.16
      │
      │ sshd
      ▼
Authentication Failures
      │
      ▼
Elastic SIEM
      │
      │ 4 matching events
      ▼
Threshold >= 3
      │
      ▼
Detection Rule Triggered
      │
      ▼
SOC Alert
```

---

## 9. Detection Validation

The detection was validated using a controlled attack against the project Linux endpoint.

Validation result:

```text
Attack activity:
CONFIRMED

Authentication failures:
CONFIRMED

Required threshold:
3

Observed matching events:
4

Detection triggered:
CONFIRMED

Alert generated:
CONFIRMED
```

---

## 10. Compromise Assessment

The detection alert confirms brute-force authentication activity.

It does not by itself establish successful account compromise.

The controlled Hydra validation reported:

```text
5 tries
0 valid password found
```

Therefore:

```text
Brute-force activity:
CONFIRMED

Detection:
CONFIRMED

Successful SSH authentication:
NOT OBSERVED
```

---

## 11. Evidence Correlation

The alert was correlated with:

```text
Attack Evidence
      ↓
Linux SSH Journal
      ↓
Elastic Authentication Events
      ↓
Detection Rule
      ↓
Alert
```

This provides the SOC investigation chain from attack execution through automated detection.

---

## 12. Evidence Status

**Detection Alert Evidence: VERIFIED**

The Project 01 detection rule successfully generated an alert from the controlled SSH authentication-failure activity originating from `192.168.1.10`.


