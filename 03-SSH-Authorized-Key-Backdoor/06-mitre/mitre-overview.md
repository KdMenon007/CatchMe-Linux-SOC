# MITRE ATT&CK Mapping Overview

## Purpose
Map observed behavior in Project 03 to MITRE ATT&CK techniques using the existing attack-execution notes, hunting findings, and screenshots. Mappings describe behavior; they do not prove that a SIEM rule generated an alert.

## Environment
- Source: Kali, `192.168.1.10`
- Endpoint: `soc-linux`, `192.168.1.16`
- Account observed: `socadmin`
- Historical activity date: 2026-10-03
- Timezone: Asia/Kolkata (IST, UTC+05:30)

## Evidence-backed mappings

| ATT&CK technique | Assessment | Evidence and limitation |
|---|---|---|
| T1110.001 — Password Guessing | Supported by supplied scenario evidence | Attack/Telemetry screenshots and execution notes document seven SSH password attempts and credential discovery. Password material remains redacted. |
| T1021.004 — SSH | Supported by supplied scenario evidence | Initial SSH access and later public-key re-entry are documented by endpoint and Elastic evidence. |
| T1098.004 — SSH Authorized Keys | Supported by endpoint evidence | Authorized-keys file line count changed from one to two; later successful public-key re-entry was observed. Exact target-path correlation in indexed Auditd data remains unresolved. |

## Discovery techniques requiring screenshot-level confirmation
The post-compromise discovery screenshot is documented, and prior notes mention host/kernel details, user/group identity, home directory, IP configuration, process list, SSH sessions, and listening ports. This summary alone does not preserve exact commands or enough detail to assign every discovery technique with high confidence.

Potential mappings to validate against the original image before treating them as confirmed:
- T1082 — System Information Discovery
- T1033 — System Owner/User Discovery
- T1057 — Process Discovery
- T1016 — System Network Configuration Discovery
- T1049 — System Network Connections Discovery

Do not map T1087 — Account Discovery unless the evidence shows discovery of accounts beyond the current user's identity/group information.

## Detection and response are separate questions
- The custom detection rule is configured for T1098.004.
- Historical alert generation for the October 3 window remains not established.
- The reviewed indexed data did not expose a matching authorized-keys target-path record.
- Containment and remediation completion are not established.

## Evidence references
- `../09-Screenshots/Attack/P03-ATT-01-credential-discovery.png`
- `../09-Screenshots/Attack/P03-ATT-02-ssh-initial-access.png`
- `../09-Screenshots/Attack/P03-ATT-03-post-compromise-discovery.png`
- `../09-Screenshots/Attack/P03-ATT-04-authorized-key-persistence.png`
- `../09-Screenshots/Attack/P03-ATT-06-key-based-reentry.png`
- `../09-Screenshots/Hunting/P03-HUNT-02-14-45-53-publickey-event.png`
- `../09-Screenshots/Hunting/P03-HUNT-06-auditd-persistence-telemetry.png`
