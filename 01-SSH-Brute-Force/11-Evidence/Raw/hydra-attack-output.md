# Hydra SSH Brute-Force Attack Evidence

## Project

**CatchMe Linux SOC — Project 01: SSH Brute Force → Compromise**

---

## 1. Attack Details

| Attribute | Value |
|---|---|
| Attack | SSH Brute Force |
| Attacker | `192.168.1.10` |
| Target | `192.168.1.16` |
| Target Host | `soc-linux` |
| Target Account | `socadmin` |
| Service | SSH |
| Tool | Hydra |
| Validation Date | 2026-10-01 |
| Start Time | 08:57:17 IST |
| Finish Time | 08:57:26 IST |

---

## 2. Password List

The controlled test password list contained five intentionally incorrect passwords:

```text
WrongPass01!
WrongPass02!
WrongPass03!
WrongPass04!
WrongPass05!
```
The password list was created specifically for the controlled laboratory validation.

---

## 3. Attack Command

```bash
hydra -l socadmin -P /tmp/project01-passwords.txt -t 2 -f ssh://192.168.1.16
```

---

## 4. Attack Result

```text
5 tries
0 valid password found
```

The controlled attack generated SSH authentication failures against the `socadmin` account.

---

## 5. Attack Timeline

```text
08:57:17 IST
    │
    ├── Hydra execution started
    │
    ├── SSH authentication attempts
    │
    ├── Linux sshd recorded authentication failures
    │
    ├── Elastic Agent collected the resulting telemetry
    │
    └── 08:57:26 IST
        Hydra execution completed
```

---

## 6. Attack Outcome

| Validation                | Result         |
| ------------------------- | -------------- |
| SSH brute-force activity  | Confirmed      |
| Authentication failures   | Confirmed      |
| Source IP                 | `192.168.1.10` |
| Target IP                 | `192.168.1.16` |
| Target account            | `socadmin`     |
| Attempts                  | 5              |
| Valid password found      | 0              |
| Successful SSH compromise | Not observed   |
| Elastic telemetry         | Confirmed      |
| Detection alert           | Generated      |

---

## 7. Evidence Correlation

The attack execution was correlated with Linux and Elastic evidence using:

```text
Kali / Hydra
192.168.1.10
      ↓
SSH Authentication Attempts
      ↓
Linux sshd
      ↓
Authentication Failures
      ↓
Elastic SIEM
      ↓
Detection Alert
```

---

## 8. Investigation Note

This artifact documents the **actual controlled validation result**.

The project title remains:

**SSH Brute Force → Compromise**

However, the observed validation run did **not** result in successful SSH compromise because no valid password was found.

---

## 9. Evidence Status

**Attack Evidence: VERIFIED**

**Attack Type:** Controlled SSH brute-force simulation

**Result:** Authentication failures generated; no successful credential compromise observed.


