# CatchMe Linux SOC

A hands-on Linux Security Operations Center laboratory
focused on attack simulation, telemetry analysis, threat
hunting, detection engineering, incident investigation,
and response using Elastic Security.

## Lab Architecture

Linux Endpoint
      ↓
Elastic Agent
      ↓
Fleet Server
      ↓
Elastic SIEM
      ↓
Threat Hunting
      ↓
Detection
      ↓
Investigation
      ↓
MITRE ATT&CK
      ↓
Incident Response

## Environment

| Component | Host | Purpose |
|---|---|---|
| Elastic SIEM | 192.168.1.11 | SIEM / Kibana |
| Linux Endpoint | 192.168.1.16 | Attack & telemetry |
| Fleet Server | Elastic SIEM | Agent management |

## Portfolio

25 Linux security investigations covering:

- Initial Access
- Execution
- Persistence
- Privilege Escalation
- Defense Evasion
- Credential Access
- Discovery
- Command and Control
- Impact

## SOC Workflow

Attack
→ Telemetry
→ Threat Hunt
→ Detection
→ Alert
→ Investigation
→ MITRE ATT&CK
→ Incident Report
→ Remediation
