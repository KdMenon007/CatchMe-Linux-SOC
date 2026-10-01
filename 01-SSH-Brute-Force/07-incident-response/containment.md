# Containment Plan

## Project

**Project:** CatchMe Linux SOC — Project 01  
**Incident ID:** `CATCHME-LNX-001`  
**Scenario:** SSH Brute Force Detection  
**Target:** `soc-linux` (`192.168.1.16`)  
**Attacker:** Kali (`192.168.1.10`)  
**Target Account:** `socadmin`  
**Service:** SSH (`TCP/22`)

---

# 1. Objective

This document defines the containment workflow for the SSH password-guessing activity detected during Project 01.

The project attack was conducted inside the controlled CatchMe lab.

No destructive containment action was performed against the lab systems because the objective was to validate:

```text
Attack
↓
Telemetry
↓
Detection
↓
Alert
↓
Investigation
```
Containment actions below are therefore documented as response procedures and recommendations rather than completed production actions.

---

# 2. Incident Trigger

The containment process begins after the Elastic detection generated:

```text
Rule:
CatchMe - Linux SSH Brute Force Detection

Source:
192.168.1.10

Target:
soc-linux / 192.168.1.16

Account:
socadmin

Service:
SSH / TCP 22

Severity:
Medium

Risk Score:
47

Threshold:
>= 3 authentication failures

Observed:
4 matching events
```

---

# 3. Containment Decision Flow

```text
                 Elastic Alert
                       |
                       v
              Validate Alert
                       |
                       v
              Identify Source
                       |
                       v
              Identify Target
                       |
                       v
             Check Authentication
                       |
             +---------+---------+
             |                   |
             v                   v
          Failure              Success
             |                   |
             v                   v
       Assess Risk          Escalate
             |                   |
             v                   v
       Containment          Immediate
       Recommendation       Investigation
```

For Project 01:

```text
Authentication
     ↓
Failure
     ↓
No successful credential demonstrated
     ↓
Containment assessment
```

---

# 4. Step 1 — Validate the Source

Confirm the source address associated with the alert:

```text
192.168.1.10
```

Correlate the source across:

* Elastic alert
* Elastic authentication events
* Linux SSH logs
* Attack-side evidence

The Project 01 investigation correlated the source to the controlled Kali attacker.

## Status

**Completed**

---

# 5. Step 2 — Validate the Target

Confirm the destination:

```text
Hostname: soc-linux
IP: 192.168.1.16
```

Confirm that the targeted service was:

```text
SSH
TCP/22
```

The endpoint SSH service was confirmed to be listening before the simulation.

## Status

**Completed**

---

# 6. Step 3 — Validate the Target Account

The targeted account was:

```text
socadmin
```

Review:

```text
Authentication history
Successful SSH logins
Failed SSH authentication
Unexpected source addresses
Recent account activity
SSH key changes
Privilege changes
```

Project 01 demonstrated repeated failed authentication attempts against the account.

## Status

**Completed for attack validation**

---

# 7. Step 4 — Check for Successful Authentication

Before taking further containment action, determine whether the attack resulted in successful authentication.

Evidence from the controlled Hydra run:

```text
0 valid password found
```

Elastic investigation:

```text
event.action = authentication_failure
```

No successful authentication was demonstrated for the attack window.

## Status

**Completed**

---

# 8. Step 5 — Check for Post-Authentication Activity

If successful authentication had occurred, investigate:

```text
Shell activity
Command execution
Privilege escalation
Persistence
Network connections
File creation
Credential access
Data access
```

For Project 01, no post-authentication attacker activity was demonstrated.

## Status

**Completed — No post-authentication activity demonstrated**

---

# 9. Step 6 — Source Isolation

## Production Recommendation

If the source is confirmed malicious or unauthorized, the SOC may consider:

```text
Block source IP
Restrict SSH access
Apply firewall controls
Apply network ACL controls
Isolate the originating endpoint
```

The exact containment mechanism depends on the organization's network architecture and incident-response procedures.

## Project 01

The Kali attacker is an authorized lab system.

Therefore:

```text
Source isolation was NOT performed.
```

## Status

**Recommended — Not Performed**

---

# 10. Step 7 — SSH Exposure Reduction

Recommended production controls include:

```text
Restrict SSH to trusted management networks
Use firewall allowlists
Avoid unnecessary Internet exposure
Limit administrative access
Review SSH authentication methods
Monitor privileged SSH access
```

Project 01 did not modify the endpoint's production-style SSH configuration.

## Status

**Recommended — Not Performed**

---

# 11. Step 8 — Account Protection

If repeated attacks target a privileged account, the SOC can evaluate:

```text
Password reset
Credential rotation
Account lockout
Temporary account restriction
MFA where supported
SSH key review
Removal of unauthorized keys
Privileged-access review
```

These actions should be based on confirmed risk and organizational procedures.

For Project 01:

```text
No credential compromise was demonstrated.
```

No forced credential reset was performed.

## Status

**Recommended — Not Performed**

---

# 12. Step 9 — Evidence Preservation

Containment should not destroy evidence required for investigation.

Preserve:

```text
Elastic alert
Elastic authentication events
Linux SSH journal
Kali attack output
Detection configuration
Investigation timeline
Relevant screenshots
MITRE mapping
Incident report
```

Evidence structure:

```text
evidence/
├── raw/
├── sanitized/
└── hashes/
```

Before GitHub publication:

```text
Remove credentials
Remove private keys
Remove tokens
Remove secrets
Remove unrelated personal information
Sanitize sensitive infrastructure information where required
```

## Status

**Evidence preservation completed**

---

# 13. Step 10 — Persistence Check

Following a successful compromise, the SOC should review common persistence locations.

For this Linux investigation, review areas such as:

```text
SSH authorized keys
Cron jobs
Systemd services
Systemd timers
Shell startup files
Suspicious binaries
Unexpected users
Sudo configuration
```

Project 01 did not demonstrate successful compromise or persistence.

Therefore, no persistence mechanism was attributed to this attack.

## Status

**Assessment completed**

---

# 14. Step 11 — Network Containment

For a confirmed production incident, possible controls include:

```text
Firewall block
Network ACL
SSH allowlist
VLAN isolation
Endpoint isolation
Management-network restriction
```

The containment mechanism should preserve required administrative access and avoid unnecessary disruption.

Project 01 did not require network isolation because the attacker was an authorized lab VM.

## Status

**Recommended — Not Performed**

---

# 15. Step 12 — Detection-Level Containment

The Elastic detection itself provides an additional SOC control.

Observed behavior:

```text
Authentication failures
        ↓
Source aggregation
        ↓
Threshold >= 3
        ↓
Elastic Alert
```

This allows the SOC to identify repeated SSH authentication activity without waiting for successful compromise.

Project 01 successfully validated this detection.

## Status

**Completed**

---

# 16. Containment Decision Matrix

| Condition                    | Recommended Response              | Project 01       |
| ---------------------------- | --------------------------------- | ---------------- |
| Repeated SSH failures        | Investigate source                | Completed        |
| Unauthorized source          | Restrict/block source             | Not applicable   |
| Successful login             | Escalate investigation            | Not observed     |
| Privileged account targeted  | Review account                    | Completed        |
| Credential compromise        | Rotate credentials                | Not demonstrated |
| Post-auth activity           | Investigate host                  | Not demonstrated |
| Persistence                  | Investigate persistence locations | Not attributed   |
| Confirmed malicious endpoint | Isolate endpoint                  | Not performed    |
| Lab attacker                 | Preserve lab environment          | Applied          |

---

# 17. Containment Evidence Chain

```text
Elastic Alert
      ↓
Source Validation
      ↓
Target Validation
      ↓
Account Validation
      ↓
Authentication Check
      ↓
Post-Authentication Check
      ↓
Risk Assessment
      ↓
Containment Decision
```

Project 01:

```text
Alert
 ↓
192.168.1.10 identified
 ↓
soc-linux identified
 ↓
socadmin identified
 ↓
Authentication failures confirmed
 ↓
0 valid password found
 ↓
No compromise demonstrated
 ↓
No destructive containment performed
```

---

# 18. Production vs Lab Actions

| Action                              | Production              | Project 01            |
| ----------------------------------- | ----------------------- | --------------------- |
| Validate source                     | Yes                     | Completed             |
| Validate target                     | Yes                     | Completed             |
| Check successful authentication     | Yes                     | Completed             |
| Preserve evidence                   | Yes                     | Completed             |
| Review post-authentication activity | Yes                     | Completed             |
| Block source                        | If justified            | Not performed         |
| Isolate endpoint                    | If justified            | Not performed         |
| Reset credentials                   | If compromise suspected | Not performed         |
| Restrict SSH                        | If required             | Not performed         |
| Review persistence                  | Yes when appropriate    | Assessed              |
| Tune detection                      | Yes                     | Completed/recommended |

---

# 19. Containment Completion Criteria

Containment can be considered complete when:

```text
[ ] Source identified
[ ] Target identified
[ ] Account identified
[ ] Authentication result confirmed
[ ] Successful login checked
[ ] Post-authentication activity checked
[ ] Evidence preserved
[ ] Persistence assessed where appropriate
[ ] Containment decision documented
[ ] Production containment performed if required
[ ] Detection remains operational
```

Project 01 evidence supports:

```text
[x] Source identified
[x] Target identified
[x] Account identified
[x] Authentication result confirmed
[x] Successful login checked
[x] Post-authentication activity checked
[x] Evidence preserved
[x] Persistence assessed
[x] Containment decision documented
[x] Detection remains operational
```

Production isolation/blocking was intentionally not performed.

---

# 20. Final Containment Assessment

The Project 01 attack was contained at the investigation level because:

```text
Repeated SSH authentication failures
        ↓
Detected by Elastic
        ↓
Source identified
        ↓
Target identified
        ↓
Account identified
        ↓
Authentication remained unsuccessful
        ↓
No successful compromise demonstrated
```

No destructive containment action was required for the controlled lab scenario.

The documented production containment procedures provide the next response layer if a future project demonstrates successful authentication or host compromise.

---

# 21. Project 01 Containment Status

| Component                       | Status        |
| ------------------------------- | ------------- |
| Alert validation                | Complete      |
| Source validation               | Complete      |
| Target validation               | Complete      |
| Account validation              | Complete      |
| Successful-authentication check | Complete      |
| Post-authentication check       | Complete      |
| Evidence preservation           | Complete      |
| Persistence assessment          | Complete      |
| Containment decision            | Complete      |
| Lab source blocking             | Not performed |
| Endpoint isolation              | Not performed |
| Credential reset                | Not performed |
| SSH configuration change        | Not performed |
| Production containment          | Not performed |
| Containment documentation       | Complete      |


