# Technique-to-Evidence Mapping

| Technique | Behavior | Primary evidence | Supporting evidence | Status / caveat |
|---|---|---|---|---|
| T1110.001 | Password Guessing | `../09-Screenshots/Attack/P03-ATT-01-credential-discovery.png` | `../09-Screenshots/Telemetry/P03-TEL-01-ssh-password-attack.png` | Supported by scenario evidence; seven attempts noted; credentials redacted |
| T1021.004 | SSH | `../09-Screenshots/Attack/P03-ATT-02-ssh-initial-access.png` | `../09-Screenshots/Telemetry/P03-TEL-02-ssh-initial-access.png`; `../09-Screenshots/Hunting/P03-HUNT-02-14-45-53-publickey-event.png` | Supported by initial access and successful public-key re-entry |
| T1098.004 | SSH Authorized Keys | `../09-Screenshots/Attack/P03-ATT-04-authorized-key-persistence.png` | `../09-Screenshots/Attack/P03-ATT-05-authorized-key-verification.png`; `../09-Screenshots/Detection/P03-DET-05-authorized-keys-path-no-results.png` | Endpoint file-state change supported; indexed target-path correlation unresolved |
| T1082 | System Information Discovery | `../09-Screenshots/Attack/P03-ATT-03-post-compromise-discovery.png` | `../09-Screenshots/Telemetry/P03-TEL-03-post-compromise-discovery.png` | Candidate only; inspect image for exact system-information commands/output |
| T1033 | System Owner/User Discovery | `../09-Screenshots/Attack/P03-ATT-03-post-compromise-discovery.png` | Same post-compromise discovery screenshots | Candidate only; confirm visible output before treating as final |
| T1057 | Process Discovery | `../09-Screenshots/Attack/P03-ATT-03-post-compromise-discovery.png` | `../09-Screenshots/Telemetry/P03-TEL-03-post-compromise-discovery.png` | Candidate only; confirm process-list evidence in image |
| T1016 | System Network Configuration Discovery | `../09-Screenshots/Attack/P03-ATT-03-post-compromise-discovery.png` | Same post-compromise discovery screenshots | Candidate only; confirm interface/IP configuration output |
| T1049 | System Network Connections Discovery | `../09-Screenshots/Attack/P03-ATT-03-post-compromise-discovery.png` | Same post-compromise discovery screenshots | Candidate only; confirm listening connections/sessions output |

## Evidence that must not be conflated
- `log.file.path: /var/log/audit/audit.log` identifies the source log, not the target authorized-keys file.
- An indexed `write` syscall by `/usr/sbin/sshd` does not alone prove a write to `authorized_keys`.
- A technique mapping does not prove that a detection alert fired.
- The reviewed alert views with no results do not establish that no alert existed outside the selected time range or view.
