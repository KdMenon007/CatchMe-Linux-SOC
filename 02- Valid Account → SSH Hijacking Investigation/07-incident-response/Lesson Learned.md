# Lessons Learned

## Project 02 — Valid Account → SSH Hijacking

## 1. Objective

This document records the lessons identified during the Project 02 valid-account SSH investigation, detection, containment, and remediation workflow.

The lessons are based on the actual activity and validation performed in the CatchMe Linux SOC laboratory.

---

## 2. Attack Scenario

The project simulated abuse of a valid Linux account through SSH.

The controlled attack path involved:

```text
Kali
192.168.1.10
    |
    | SSH / credential attack
    v
soc-linux
192.168.1.16
    |
    | Valid account
    v
socadmin
```

The investigation demonstrated that valid-account activity can generate legitimate-looking SSH authentication and session telemetry while still requiring SOC investigation.

---

## 3. Lesson 01 — Valid Accounts Can Bypass Simple Authentication-Based Assumptions

The activity used the legitimate `socadmin` account.

The authentication event therefore contained a legitimate username rather than an obviously malicious account name.

This demonstrates that monitoring only for unknown usernames is insufficient for detecting account abuse.

### SOC Lesson

Authentication monitoring should correlate:

* username
* source IP
* source host
* authentication method
* SSH process
* authentication result
* session activity
* historical behavior

---

## 4. Lesson 02 — Source IP Correlation Is Important

The activity originated from the controlled Kali system:

```text
192.168.1.10
```

The target endpoint was:

```text
192.168.1.16
```

Correlating the source IP with the account and SSH process provided additional investigation context.

### SOC Lesson

Successful authentication should not automatically be treated as benign.

The source system and source IP should be investigated together with the account identity.

---

## 5. Lesson 03 — Authentication Events Should Be Correlated With Session Activity

Elastic telemetry provided multiple SSH-related events, including authentication and session-related activity.

The investigation therefore used multiple event types rather than relying on a single event.

Relevant fields included:

```text
event.action
process.name
host.name
source.ip
source.port
user.name
```

### SOC Lesson

A useful SSH investigation should correlate:

```text
Authentication
      +
SSH Process
      +
Session Activity
      +
Source IP
      +
User
```

This provides stronger investigative context than a single authentication event.

---

## 6. Lesson 04 — Detection Rules Need Real Validation

The detection rule:

```text
CatchMe - Linux SSH Valid Account Activity
```

was created and subsequently validated using fresh SSH activity.

The rule generated a real Elastic alert.

The alert contained:

* Host: `soc-linux`
* Process: `sshd`
* Source IP: `192.168.1.10`
* User: `socadmin`
* Event action: `ssh_login`
* Severity: Medium
* Risk score: 47

### SOC Lesson

A detection rule should not be considered complete merely because the KQL query is syntactically valid.

It should be tested against fresh telemetry and verified through the actual alert workflow.

---

## 7. Lesson 05 — Investigation Must Follow the Alert Back to the Original Event

The investigation workflow moved from:

```text
Alert
  ↓
Original Event
  ↓
Authentication Correlation
  ↓
SSH Session Activity
  ↓
Source/User Correlation
```

This allowed the alert to be investigated using the underlying telemetry.

### SOC Lesson

SOC analysts should preserve the relationship between:

* detection alert
* original event
* related authentication events
* session activity
* endpoint evidence

---

## 8. Lesson 06 — Linux Native Logs Remain Valuable

Elastic telemetry was correlated with Linux-native evidence including:

```text
/var/log/auth.log
journalctl -u ssh
```

The native logs provided additional context for SSH authentication and session behavior.

### SOC Lesson

SIEM telemetry should be correlated with endpoint-native logs when investigating authentication activity.

This can help validate event meaning and avoid interpreting individual SIEM events in isolation.

---

## 9. Lesson 07 — Event Semantics Must Be Verified

During the investigation, Elastic produced multiple SSH-related event actions.

Not every event action should automatically be interpreted as an independent successful interactive login.

The investigation therefore compared Elastic telemetry with the underlying Linux authentication logs.

### SOC Lesson

Analysts should validate unusual or ambiguous event sequences against the original endpoint evidence before documenting an event as a confirmed successful action.

This prevents incorrect conclusions from being added to an incident timeline.

---

## 10. Lesson 08 — Timezone Standardization Matters

The laboratory initially contained systems using different timezone representations.

The environment was subsequently standardized to:

```text
Asia/Kolkata
IST
```

Elastic timestamps can still be represented internally in UTC while Kibana displays the selected local timezone.

### SOC Lesson

Incident timelines should clearly distinguish:

* event timestamp
* displayed timezone
* underlying timestamp representation

Timezone normalization is particularly important when correlating:

```text
Kali
Linux endpoint
Elastic SIEM
```

---

## 11. Lesson 09 — Evidence Should Be Captured During the Investigation

The project captured screenshots and raw evidence during:

* attack execution
* telemetry validation
* detection
* investigation
* containment
* remediation

This avoided relying on memory after the activity had finished.

### SOC Lesson

Evidence should be captured while the relevant state is visible.

Important evidence includes:

* terminal output
* SIEM events
* alerts
* configuration state
* process state
* network connections
* remediation validation

---

## 12. Lesson 10 — Password Authentication Increased the SSH Attack Surface

The baseline configuration showed:

```text
passwordauthentication yes
```

The remediation changed this to:

```text
passwordauthentication no
```

Public-key authentication was validated before and after the change.

### SOC Lesson

Where operationally appropriate, SSH environments should prefer controlled public-key authentication over password-based remote authentication.

---

## 13. Lesson 11 — Remediation Must Preserve Administrative Access

Password authentication was not disabled until public-key authentication had been successfully established and tested.

The workflow was:

```text
Create SSH key
      ↓
Install public key
      ↓
Test key authentication
      ↓
Disable password authentication
      ↓
Reload SSH
      ↓
Test password rejection
      ↓
Test key authentication again
```

### SOC Lesson

Security hardening should be performed in a way that preserves a verified recovery or administrative path.

---

## 14. Lesson 12 — Firewall Changes Must Be Validated Before Enforcement

UFW was initially inactive.

Before enabling it, an SSH rule was created for:

```text
192.168.1.0/24
```

The rule was verified before UFW was enabled.

After activation, SSH public-key access was tested again.

### SOC Lesson

Firewall remediation should follow:

```text
Create rule
    ↓
Verify rule
    ↓
Enable enforcement
    ↓
Test legitimate access
```

This reduces the risk of unintentionally locking out administrative access.

---

## 15. Lesson 13 — Unnecessary SSH Features Should Be Reduced

X11 forwarding was enabled in the baseline:

```text
x11forwarding yes
```

It was not required for this project and was disabled:

```text
x11forwarding no
```

### SOC Lesson

Reducing unnecessary remote-access functionality can reduce the available attack surface.

---

## 16. Lesson 14 — Detection and Prevention Are Different Controls

The project demonstrated both:

### Detection

Elastic detected SSH activity through:

```text
process.name : "sshd"
source.ip : *
host.name : "soc-linux"
```

### Prevention / Hardening

The endpoint was subsequently hardened through:

* disabling password authentication
* disabling X11 forwarding
* enabling UFW
* restricting SSH access to the lab subnet

### SOC Lesson

A mature SOC workflow should connect:

```text
Detection
   ↓
Investigation
   ↓
Containment
   ↓
Remediation
   ↓
Validation
```

Detection alone does not complete the incident-response lifecycle.

---

## 17. Lesson 15 — Preserve the Difference Between Observed and Assumed Activity

The project did not treat every SSH-related event as proof of additional attacker behavior.

Only activity supported by actual endpoint or Elastic evidence was documented as an observed result.

No successful:

* privilege escalation
* persistence
* credential theft
* data exfiltration
* destructive activity

was demonstrated as part of this project.

### SOC Lesson

Incident reports should clearly separate:

```text
Observed
Confirmed
Not observed
Not tested
```

This prevents unsupported conclusions from entering the final incident record.

---

## 18. Detection Engineering Improvements

Based on this project, future detection logic can be expanded to correlate:

* successful SSH authentication
* unusual source IP
* new source IP for a known account
* repeated authentication failures followed by success
* SSH session creation
* post-authentication command execution
* privilege escalation after SSH login
* persistence changes after SSH access

These should be implemented and validated as separate detections rather than assuming one rule covers every SSH abuse scenario.

---

## 19. Operational Improvements

Future CatchMe Linux SOC projects should continue to:

1. Standardize timezones before attack execution.
2. Verify lab IP mappings before every attack.
3. Capture evidence during each stage.
4. Correlate SIEM telemetry with native Linux logs.
5. Validate detection rules using fresh activity.
6. Preserve raw evidence before creating final documentation.
7. Test remediation controls after applying them.
8. Keep screenshots organized by project stage.
9. Avoid documenting activity that was not actually observed.
10. Preserve reproducible KQL queries with each investigation.

---

## 20. Project 02 Takeaway

Project 02 demonstrated the complete SOC workflow for valid-account SSH activity:

```text
Valid Account Activity
        ↓
SSH Authentication
        ↓
Endpoint Telemetry
        ↓
Elastic SIEM
        ↓
Threat Hunting
        ↓
Detection
        ↓
Alert
        ↓
Investigation
        ↓
Containment
        ↓
Remediation
        ↓
Validation
```

The main operational lesson is that valid-account SSH activity requires contextual investigation rather than relying solely on the account name or authentication success/failure state.

---

## 21. Lessons Learned Status

**Lessons Learned: COMPLETED**

Project 02 now has documented lessons covering attack behavior, telemetry, detection engineering, investigation, containment, remediation, evidence handling, and validation.

