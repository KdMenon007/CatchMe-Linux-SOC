# Detection Screenshots

This folder contains screenshots showing the **detection engineering and alert generation** for Project 01.

## Purpose

These screenshots document how the SSH brute-force behavior was converted into an Elastic detection rule and how the rule generated an alert during validation.

## Detection Process

- Create threshold rule
- Configure KQL query
- Configure threshold
- Configure schedule and look-back
- Enable the rule
- Validate the rule with the controlled attack
- Confirm alert generation

## Detection Logic

```text
authentication_failure
        +
sshd
        +
soc-linux
        +
source.ip
        +
threshold >= 3
        ↓
SSH Brute Force Alert
```
## Alert Result

* **Rule:** CatchMe - Linux SSH Brute Force Detection
* **Severity:** Medium
* **Risk Score:** 47
* **Threshold Count:** 4
* **Source IP:** `192.168.1.10`
* **Status:** Open

## Evidence

The screenshots show the actual Elastic rule configuration, rule status, validation state, and generated alert.

## Important Note

The detection confirms repeated SSH authentication failures.

It does **not** demonstrate a successful account compromise.

```
```
