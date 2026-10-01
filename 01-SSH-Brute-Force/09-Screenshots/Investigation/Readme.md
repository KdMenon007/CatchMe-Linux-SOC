# Investigation Screenshots

This folder contains screenshots showing the **SOC investigation of the SSH brute-force detection alert**.

## Purpose

These screenshots document how the generated alert was validated and correlated with the underlying Linux SSH events.

## Investigation Activities

- Review generated alert
- Validate alert metadata
- Identify source IP
- Correlate authentication failure events
- Review Linux SSH journal
- Compare timestamps
- Confirm attack activity
- Validate investigation findings

## Evidence Correlation

```text
Detection Alert
      ↓
Source IP: 192.168.1.10
      ↓
Target: soc-linux
      ↓
User: socadmin
      ↓
Process: sshd
      ↓
Authentication Failures
      ↓
Linux SSH Journal
      ↓
Timeline Correlation
      ↓
Investigation Finding
```

## Key Evidence

* Source IP: `192.168.1.10`
* Target Host: `soc-linux`
* Target Account: `socadmin`
* Process: `sshd`
* Event Type: `authentication_failure`
* Detection Threshold: `4` matching events
* Alert Status: Open
* Successful Login: Not observed

