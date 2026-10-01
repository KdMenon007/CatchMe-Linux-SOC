# Threat Hunting Screenshots

This folder contains screenshots of the **KQL threat-hunting activity** performed during Project 01.

## Purpose

These screenshots show how the SOC analyst searched and correlated SSH authentication events in Elastic SIEM.

## Hunting Activities

- Search for SSH authentication failures
- Filter events by source IP
- Filter events by Linux endpoint
- Correlate `sshd` activity
- Validate the observed attack activity

## Main Fields

- `event.action`
- `source.ip`
- `host.name`
- `process.name`
- `user.name`
- `@timestamp`

## Hunting Flow

```text
Raw Events
   ↓
KQL Query
   ↓
Filter Authentication Failures
   ↓
Identify Source IP
   ↓
Correlate SSH Activity
   ↓
Validate Finding
```
## Evidence

The screenshots provide visual evidence of the KQL queries and the resulting SSH authentication events observed in Elastic SIEM.

## Important Note

Threat hunting identifies and validates suspicious activity.

The hunting results do not by themselves prove that the SSH account was successfully compromised.


