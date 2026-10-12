# Incident Response Validation

## Current validation matrix

| Item | Current evidence | Status |
|---|---|---|
| Endpoint identity | `soc-linux`, `192.168.1.16` | Verified in captured output |
| Active operator session | `192.168.1.10` to `192.168.1.16:22` | Observed; matches current lab session |
| Authorized-key file structure | Two public-key lines plus one blank line | Verified |
| Key authorization | Operator unsure whether either fingerprint is expected | Unresolved |
| File permissions/owner | `600`, owner/group `socadmin`; `.ssh` directory mode `700` | Observed |
| Auditd service/status | Active; enabled 1; lost 0; backlog 0 in captured output | Observed at capture time |
| Elastic Agent service | Active in captured output | Observed at capture time |
| Historical detection alert | No results in reviewed Oct 3 views; execution history does not cover all earlier time | Not established |
| Target-path SIEM correlation | No matching target-path record in reviewed searches | Unresolved |
| Containment | No key removal or session termination recorded | Not performed / not verified |
| Eradication | No confirmed unauthorized key identified or removed | Not performed / not verified |
| Recovery | No post-remediation validation performed | Not verified |

## Evidence limitations
Service state and screenshots are point-in-time observations. They do not prove continuous health or end-to-end ingestion. Do not promote a status to “complete” without new evidence tied to the exact response action.
