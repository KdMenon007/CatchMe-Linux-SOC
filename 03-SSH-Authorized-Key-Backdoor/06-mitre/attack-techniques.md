# ATT&CK Techniques and Confidence

## Confirmed by supplied scenario evidence

### T1110.001 — Password Guessing
**Confidence:** Supported by supplied scenario evidence.
**Basis:** The attack execution record documents seven SSH password attempts and valid credential discovery; screenshots cover the credential-discovery and SSH password-attack phases.
**Limit:** The password is intentionally redacted. Do not expose credentials or wordlist contents.

### T1021.004 — SSH
**Confidence:** Supported by supplied scenario evidence.
**Basis:** Initial SSH access and successful public-key re-entry are documented in endpoint/Elastic evidence.
**Limit:** This mapping does not itself establish alert generation or full packet-level network reconstruction.

### T1098.004 — SSH Authorized Keys
**Confidence:** Supported by endpoint evidence.
**Basis:** The authorized-keys file line count changed from one to two, with mode `600` and owner/group `socadmin`; a successful public-key re-entry was later observed.
**Limit:** Historical indexed Auditd path correlation and the alert outcome remain unresolved.

## Discovery candidates — verify original screenshot detail first
The project notes mention post-access discovery of host/kernel information, user/group identity, home directory, IP configuration, processes, SSH sessions, and listening ports. Before marking any of the following as confirmed, review `P03-ATT-03-post-compromise-discovery.png` and record the exact visible command/output:
- T1082 — System Information Discovery
- T1033 — System Owner/User Discovery
- T1057 — Process Discovery
- T1016 — System Network Configuration Discovery
- T1049 — System Network Connections Discovery

T1087 — Account Discovery is not assigned as confirmed because the currently summarized evidence does not establish enumeration of other accounts.

## Confidence language
Use “supported by supplied scenario evidence” when screenshots and prior notes support the behavior. Use “candidate—verify screenshot” where exact output has not been transcribed. Avoid numerical confidence scores without a defined scoring method.
