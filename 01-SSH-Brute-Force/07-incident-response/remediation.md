# Remediation Plan

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Incident ID:** `CATCHME-LNX-001`  
**Scenario:** SSH Brute Force Detection  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Service:** SSH (`TCP/22`)

---

# 1. Remediation Objective

This document defines remediation actions following the controlled SSH password-guessing activity detected during Project 01.

The remediation focuses on:

- reducing SSH attack surface
- strengthening authentication
- protecting privileged accounts
- improving brute-force detection
- improving investigation capability
- reducing false positives
- validating that security controls continue to work

No production remediation change was performed during Project 01 unless explicitly identified as completed.

---

# 2. Remediation Summary

| Area | Remediation | Project 01 Status |
|---|---|---|
| SSH authentication | Review authentication methods | Recommended |
| SSH exposure | Restrict SSH access | Recommended |
| Privileged accounts | Review administrative access | Recommended |
| Brute-force protection | Rate limiting / blocking | Recommended |
| SIEM detection | Maintain brute-force rule | Completed |
| Detection tuning | Review false positives | Recommended |
| Failure → success correlation | Add detection | Recommended |
| Logging | Maintain SSH authentication telemetry | Validated |
| Account monitoring | Monitor privileged accounts | Recommended |
| Evidence handling | Preserve and sanitize evidence | Completed |
| Validation | Re-test detection | Completed |

---

# 3. SSH Authentication Hardening

## Objective

Reduce the likelihood that repeated password guessing can result in account compromise.

Recommended controls:

```text
Prefer SSH key-based authentication
Review password authentication requirements
Restrict administrative SSH access
Review root SSH configuration
Use strong authentication controls
Remove unnecessary SSH access
```
The exact configuration should follow the organization's security requirements.

## Project 01 Status

**Recommended — Not Performed**

No SSH authentication configuration was changed as part of this project.

---

# 4. Restrict SSH Exposure

## Objective

Reduce the number of systems and networks that can reach SSH.

Recommended controls:

```text
Restrict SSH to trusted management networks
Use firewall allowlists
Avoid unnecessary external exposure
Limit administrative source networks
Review TCP/22 exposure regularly
```

Project 01 intentionally retained SSH access because SSH was required for the lab scenario.

## Status

**Recommended — Not Performed**

---

# 5. Protect Privileged Accounts

The targeted account was:

```text
socadmin
```

The account has administrative privileges in the CatchMe Linux lab.

Recommended controls:

```text
Review privileged account membership
Minimize administrative accounts
Use separate administrative identities
Monitor privileged SSH authentication
Review authentication failures
Review SSH keys
Review sudo activity
```

## Status

**Recommended**

No account privilege changes were made during Project 01.

---

# 6. Brute-Force Protection

The Project 01 detection successfully identified repeated authentication failures.

Additional production controls can include:

```text
Authentication rate limiting
Source-based blocking
SSH connection restrictions
Network firewall controls
Account protection mechanisms
Centralized authentication monitoring
```

These controls should be implemented according to the organization's operational requirements.

## Status

**Recommended — Not Performed**

---

# 7. Elastic Detection Improvement

The existing detection:

```text id="g1h6l5"
CatchMe - Linux SSH Brute Force Detection
```

uses:

```kql id="1xy1zj"
event.action : "authentication_failure"
and process.name : "sshd"
and host.name : "soc-linux"
```

with:

```text id="ukm6ap"
Group by: source.ip
Threshold: >= 3
Schedule: 1 minute
Look-back: 1 minute
```

The controlled test generated:

```text
4 matching events
```

and produced a:

```text
Medium
Risk Score: 47
```

alert.

## Status

**Completed**

---

# 8. Failure-to-Success Detection

A useful future improvement is to detect a sequence where multiple authentication failures are followed by a successful authentication from the same source.

Conceptual detection flow:

```text
Multiple SSH Failures
        ↓
Same Source IP
        ↓
Same Account
        ↓
Successful Authentication
        ↓
Higher-Priority Investigation
```

This can provide stronger investigative context than failure-only detection.

## Status

**Recommended — Future Detection**

---

# 9. False-Positive Reduction

The detection should distinguish malicious password guessing from legitimate administrative activity.

Potential legitimate sources include:

```text
Authorized administrators
Automation systems
Management platforms
Monitoring systems
Approved security testing
```

Potential suspicious characteristics include:

```text
High authentication-failure volume
Unknown source IP
Unexpected account
Unexpected geographic/network source
Failures across multiple accounts
Failure burst followed by success
```

Detection tuning should be based on observed lab or production baselines.

## Status

**Recommended**

---

# 10. Logging Improvements

The Project 01 investigation demonstrated that SSH authentication telemetry was available in Elastic.

Important fields included:

```text
event.action
source.ip
user.name
process.name
host.name
@timestamp
```

Maintaining these fields supports:

```text
Threat hunting
Detection
Alert investigation
Timeline reconstruction
Source correlation
MITRE ATT&CK mapping
```

## Status

**Validated**

---

# 11. Privileged SSH Monitoring

Future monitoring should prioritize authentication activity involving privileged accounts.

Example investigation dimensions:

```text
Source IP
Target host
Username
Authentication result
Authentication method
Timestamp
Process
Post-authentication activity
```

This can help identify attacks against administrative access paths.

## Status

**Recommended**

---

# 12. SSH Key Monitoring

SSH keys provide an important persistence and access-control surface.

Future monitoring should consider changes involving:

```text
~/.ssh/authorized_keys
SSH configuration
Unexpected public keys
Unexpected key ownership
New administrative SSH keys
```

Any unexpected key addition should be investigated according to the organization's response procedures.

## Status

**Recommended**

---

# 13. Post-Authentication Monitoring

A successful SSH authentication should trigger additional investigation.

Recommended correlation:

```text
Authentication Success
        ↓
User
        ↓
Source IP
        ↓
SSH Process
        ↓
Shell / Command Activity
        ↓
Privilege Activity
        ↓
File Activity
        ↓
Network Activity
```

This extends the SOC investigation from credential access into potential post-compromise behavior.

## Status

**Recommended**

---

# 14. Endpoint Hardening Review

Future Linux security reviews should consider:

```text
Unused services
SSH configuration
Privileged users
Sudo configuration
SUID/SGID binaries
Cron jobs
Systemd services
Systemd timers
SSH authorized keys
File permissions
Kernel security configuration
Logging configuration
```

The baseline collected before Project 01 provides a reference point for future comparisons.

## Status

**Recommended**

---

# 15. Detection Engineering Improvement Cycle

Project 01 establishes the following detection-development workflow:

```text
Observed Behavior
       ↓
Reliable Telemetry Fields
       ↓
Broad Threat Hunt
       ↓
Narrow KQL Query
       ↓
False-Positive Review
       ↓
Baseline Testing
       ↓
Attack Testing
       ↓
Detection Rule
       ↓
Alert
       ↓
Investigation
       ↓
Detection Tuning
```

This workflow should be reused for future CatchMe projects.

---

# 16. Remediation Validation

A remediation should not be considered complete until the control has been tested.

Recommended validation:

```text
Apply control
     ↓
Confirm configuration
     ↓
Run controlled test
     ↓
Verify endpoint telemetry
     ↓
Verify Elastic ingestion
     ↓
Run KQL hunt
     ↓
Verify detection
     ↓
Verify alert
     ↓
Document result
```

Project 01 already validated the detection component through a controlled second Hydra run.

## Status

**Detection Validation — Complete**

---

# 17. GitHub Remediation Evidence

Recommended evidence for the project:

```text
screenshots/
├── detection/
│   ├── 01-threshold-rule-configuration.png
│   ├── 03-rule-created-enabled.png
│   └── 05-brute-force-alert-generated.png
│
└── investigation/
    ├── 01-alert-overview.png
    ├── 04-source-events-correlation.png
    └── 05-linux-ssh-journal-current-attack.png
```

Supporting documentation:

```text
04-detection/detection.md
04-detection/detection-logic.md
05-investigation/investigation.md
05-investigation/findings.md
05-investigation/timeline.md
06-mitre/mitre-attack.md
07-incident-response/incident-report.md
07-incident-response/containment.md
```

---

# 18. Remediation Priority

| Priority | Area                            | Reason                                        |
| -------- | ------------------------------- | --------------------------------------------- |
| High     | SSH access restriction          | Reduces attack surface                        |
| High     | Privileged-account protection   | Protects administrative access                |
| High     | Failure → success correlation   | Helps identify possible successful compromise |
| Medium   | Brute-force protection          | Reduces repeated authentication attempts      |
| Medium   | SSH key monitoring              | Detects potential persistence/access changes  |
| Medium   | Detection tuning                | Reduces false positives                       |
| Medium   | Post-authentication correlation | Extends investigation capability              |
| Low      | Documentation refinement        | Improves repeatability                        |

These priorities describe the order of remediation areas for the lab's security workflow; they are not a production risk rating.

---

# 19. Project 01 Remediation Status

```text
SSH detection                  ✓
SSH telemetry                  ✓
Threat hunting                 ✓
Alert generation               ✓
Investigation                  ✓
Evidence preservation          ✓
Detection validation           ✓

SSH configuration hardening    Recommended
SSH exposure restriction       Recommended
Account hardening              Recommended
Brute-force prevention         Recommended
Failure → success detection    Recommended
SSH key monitoring             Recommended
Post-authentication detection  Recommended
```

---

# 20. Final Remediation Assessment

Project 01 successfully validated the SOC detection and investigation controls for repeated SSH authentication failures.

The main remediation opportunities identified are:

```text
Reduce SSH exposure
Strengthen administrative authentication
Protect privileged accounts
Add brute-force prevention controls
Detect failure → success sequences
Monitor SSH key changes
Expand post-authentication correlation
Continue detection tuning
```

No successful credential compromise or host compromise was demonstrated during Project 01.

Therefore, remediation recommendations focus on **preventing future successful SSH compromise and improving detection depth**, rather than responding to demonstrated host compromise.


