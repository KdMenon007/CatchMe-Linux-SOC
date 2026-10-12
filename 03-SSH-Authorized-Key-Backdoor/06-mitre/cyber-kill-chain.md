# Cyber Kill Chain — Scenario View

This is a high-level narrative of the lab scenario, not a claim that every Lockheed Martin Cyber Kill Chain phase was observed.

| Scenario step | Observed behavior | Evidence status |
|---|---|---|
| Initial reconnaissance / preparation | The lab attack used a known endpoint and SSH service | Exact reconnaissance outside the supplied evidence is not established |
| Credential access | SSH password attempts and valid credential discovery were documented | Supported by scenario evidence |
| Initial access | SSH password authentication/session documented | Supported by scenario evidence |
| Discovery | Post-access host/process/network discovery captured in screenshots | General activity documented; individual technique mappings require image-level confirmation |
| Persistence | Authorized-keys file line count changed from one to two | Endpoint evidence supports file-state change; indexed target-path correlation unresolved |
| Re-entry | Successful public-key SSH login from Kali to `soc-linux` | Corroborated by endpoint and Elastic evidence |
| Command and control / exfiltration | No evidence in the reviewed material establishes these phases | Not mapped |
| Impact | No evidence in the reviewed material establishes impact beyond the lab scenario | Not mapped |

## Important distinction
The Cyber Kill Chain is not interchangeable with MITRE ATT&CK. This table is a narrative sequence and should not be used to infer unobserved phases such as command and control, exfiltration, or impact.
