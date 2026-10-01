# Time Correlation Query

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

---

# 1. Query Objective

The purpose of this supporting query is to correlate timestamps across the:

- Kali attacker
- Linux endpoint
- Elastic SIEM
- SSH authentication events
- Hydra attack execution
- Detection alert generation

The laboratory systems use different timezone configurations:

| System | Timezone |
|---|---|
| Kali attacker | Asia/Kolkata (IST / UTC+05:30) |
| Linux endpoint | UTC |
| Elastic investigation view | IST |

Because the systems use different timezone representations, timestamp normalization is required before correlating attack activity.

---

# 2. Time Synchronization Validation

## Kali Attacker

The Kali system was checked using:

```bash
date --iso-8601=seconds
timedatectl
```
Observed configuration:

```text
2026-10-01T09:16:24+05:30
Timezone: Asia/Kolkata
NTP active
```

---

## Linux Endpoint

The Linux endpoint was checked using:

```bash
date --iso-8601=seconds
timedatectl
```

Observed configuration:

```text
2026-10-01T03:46:12+00:00
Timezone: Etc/UTC
NTP active
```

The systems therefore had synchronized clocks while displaying timestamps in different timezone representations.

---

# 3. Timezone Conversion

The Linux endpoint reports SSH journal timestamps in UTC.

The Elastic investigation timeline was reviewed in IST.

The conversion is:

```text
UTC + 05:30 = IST
```

Therefore:

```text
03:27:17 UTC
        ↓
08:57:17 IST
```

This timestamp is important because the Linux SSH journal and the Kali Hydra execution begin within the same attack window.

---

# 4. Attack Timeline Correlation

The second Hydra validation was executed from Kali using:

```bash
hydra -l socadmin -P /tmp/project01-passwords.txt -t 2 -f ssh://192.168.1.16
```

Observed Hydra execution:

```text
Start: 08:57:17 IST
Finish: 08:57:26 IST
Attempts: 5
Valid passwords: 0
```

Linux SSH journal evidence covered:

```text
03:27:00 UTC
        ↓
03:30:00 UTC
```

The first relevant authentication activity occurred at:

```text
03:27:17 UTC
```

which corresponds to:

```text
08:57:17 IST
```

---

# 5. Elastic Event Correlation

The corresponding Elastic authentication-failure events were observed during the investigation window:

```text
08:57:17.887 IST
08:57:17.888 IST
08:57:23.642 IST
08:57:26.518 IST
```

All four events contained the relevant correlation characteristics:

```text
Host       : soc-linux
Source IP  : 192.168.1.10
User       : socadmin
Process    : sshd
Action     : authentication_failure
```

---

# 6. Timestamp Correlation Flow

```text
KALI ATTACKER
Asia/Kolkata
08:57:17 IST
     │
     │ Hydra starts
     ▼
LINUX ENDPOINT
UTC
03:27:17 UTC
     │
     │ SSH authentication failures
     ▼
ELASTIC SIEM
IST investigation view
08:57:17.887
08:57:17.888
08:57:23.642
08:57:26.518
     │
     │ Detection threshold reached
     ▼
ALERT
08:58:02.247 IST
```

---

# 7. Linux SSH Journal Correlation

The Linux journal was queried using:

```bash
sudo journalctl -u ssh --since "2026-10-01 03:27:00" --until "2026-10-01 03:30:00" --no-pager
```

Relevant activity included:

```text
03:27:17 UTC
SSH connection / pre-authentication activity

03:27:17 UTC
Authentication failure from 192.168.1.10
User: socadmin

03:27:19 UTC
Failed password

03:27:22 UTC
Failed password

03:27:23 UTC
Connection closed / PAM authentication activity

03:27:25 UTC
Failed password

03:27:26 UTC
Connection closed / PAM authentication activity
```

The journal activity aligns with the Elastic authentication-failure events after timezone conversion.

---

# 8. Elastic Investigation Window

The investigation was performed using the following window:

```text
08:55 IST → 09:00 IST
```

This window was selected to include:

* activity immediately before the attack
* the complete Hydra execution
* authentication failures
* threshold detection
* alert generation

The observed detection timeline was:

```text
08:57:17
Attack authentication failures begin

08:57:23
Additional authentication failure

08:57:26
Final observed authentication failure

08:58:02.247
Detection alert generated
```

---

# 9. Detection Alert Timing

The detection rule was configured as:

```text
Rule:
CatchMe - Linux SSH Brute Force Detection

Type:
Threshold

Group by:
source.ip

Threshold:
>= 3

Schedule:
1 minute

Lookback:
1 minute

Severity:
Medium

Risk Score:
47
```

During the validation attack, the rule identified:

```text
Source IP:
192.168.1.10

Matching events:
4
```

The alert metadata showed:

```text
Original event time:
Oct 1, 2026 @ 08:57:26.518

Alert timestamp:
Oct 1, 2026 @ 08:58:02.247

Last detected:
Oct 1, 2026 @ 08:58:02.254
```

---

# 10. Correlation Timeline

```text
08:57:17 IST
│
├── Hydra attack starts
│
├── 03:27:17 UTC
│   Linux SSH authentication activity begins
│
├── 08:57:17.887
│   Elastic authentication failure
│
├── 08:57:17.888
│   Elastic authentication failure
│
├── 08:57:23.642
│   Elastic authentication failure
│
├── 08:57:26.518
│   Elastic authentication failure
│
├── 08:57:26
│   Hydra execution completes
│   0 valid passwords found
│
└── 08:58:02.247
    Detection alert generated
```

---

# 11. Evidence Correlation

The timestamps provide correlation between four independent evidence sources:

```text
┌─────────────────────┐
│   Kali / Hydra      │
│   Attack Execution   │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Linux SSH Journal   │
│ Authentication      │
│ Failures            │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Elastic Events      │
│ authentication_     │
│ failure             │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Detection Rule      │
│ Threshold >= 3      │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ SOC Alert           │
│ Medium / Risk 47    │
└─────────────────────┘
```

---

# 12. Investigation Significance

Timestamp correlation established that the observed authentication failures occurred during the same time window as the controlled Hydra validation.

The following attributes were consistent across the evidence:

```text
Source IP:
192.168.1.10

Destination:
192.168.1.16

Target account:
socadmin

Service:
SSH

Process:
sshd

Event type:
authentication_failure
```

The timezone difference between Kali and Linux was accounted for before comparing timestamps.

---

# 13. Attack Success Validation

Timestamp correlation confirms the attack activity but does **not** establish successful authentication.

The Hydra result showed:

```text
5 tries
0 valid password found
```

Therefore:

```text
Brute-force activity:
CONFIRMED

Authentication failures:
CONFIRMED

Detection:
CONFIRMED

Successful SSH compromise:
NOT OBSERVED
```

The project scenario remains titled:

```text
SSH Brute Force → Compromise
```

but the actual validation run demonstrated brute-force activity and detection without successful credential compromise.

---

# 14. Evidence Mapping

| Evidence Source   | Time Representation | Correlated Activity           |
| ----------------- | ------------------- | ----------------------------- |
| Kali              | IST                 | Hydra execution               |
| Linux SSH journal | UTC                 | SSH authentication failures   |
| Elastic events    | IST                 | Authentication failure events |
| Detection alert   | IST                 | Threshold detection           |
| Hydra result      | IST                 | Attack completion             |

---

# 15. Investigation Conclusion

Timestamp normalization successfully correlated the controlled attack across the attacker, endpoint, SIEM telemetry, and detection layers.

The sequence was:

```text
Hydra execution
      ↓
SSH authentication failures
      ↓
Linux journal evidence
      ↓
Elastic authentication_failure events
      ↓
Threshold reached
      ↓
SOC detection alert
```

The correlated evidence supports the conclusion that:

> A controlled SSH brute-force simulation was executed from `192.168.1.10` against `soc-linux` (`192.168.1.16`), generated multiple authentication failures, was ingested into Elastic SIEM, and triggered the configured detection rule.

No successful SSH authentication was observed during this validation.

---

# 16. Evidence Path

Supporting evidence for this correlation is stored under:

```text
evidence/
screenshots/
queries/
```

Relevant screenshots include:

```text
screenshots/attack/
02-kali-hydra-detection-test.png

screenshots/telemetry/
01-elastic-ssh-authentication-events.png
01-linux-ssh-journal-attack-evidence.png

screenshots/detection/
05-brute-force-alert-generated.png

screenshots/investigation/
04-source-events-correlation.png
05-linux-ssh-journal-current-attack.png
```

---

# 17. Final Finding

```text
Time correlation:
VALIDATED

Timezone difference:
ACCOUNTED FOR

Attack execution:
CORRELATED

Linux SSH telemetry:
CORRELATED

Elastic telemetry:
CORRELATED

Detection alert:
CORRELATED

Successful compromise:
NOT OBSERVED
```

---

# 18. Status

**Time Correlation: COMPLETED**

The supporting timeline provides the temporal relationship between attack execution, endpoint telemetry, Elastic SIEM events, and detection alert generation for Project 01.

