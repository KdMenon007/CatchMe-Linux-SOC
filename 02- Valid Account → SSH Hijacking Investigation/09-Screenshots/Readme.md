# Project 02 — Screenshot Evidence Index

## 09-Screenshots

This directory contains the visual evidence captured during the Project 02 **Valid Account → SSH Hijacking** investigation.

All screenshots are based on activity observed in the CatchMe Linux SOC lab.

---

## Screenshot Structure

```text
09-Screenshots/
├── Attack/
├── Telemetry/
├── Hunting/
├── Detection/
├── Investigation/
├── Incident-Response/
└── Mitre/
```

---

# 01 — Attack

```text
Attack/
├── 01-hydra-credential-discovery.png
└── 02-ssh-valid-account-access.png
```

### 01-hydra-credential-discovery.png

Shows the controlled credential-discovery stage performed against the lab SSH service using Hydra.

Purpose:

* Demonstrates attacker-side credential discovery.
* Establishes the transition from password guessing to valid-account access.
* Uses the isolated Project 02 lab environment.

### 02-ssh-valid-account-access.png

Shows successful SSH access to `soc-linux` using the `socadmin` account.

Purpose:

* Demonstrates valid-account abuse.
* Confirms SSH access from the Kali attacker.
* Supports the Project 02 attack scenario.

---

# 02 — Telemetry

```text
Telemetry/
├── 01-linux-raw-auth-log.png
├── 02-linux-ssh-journal.png
├── 03-elastic-authentication-chain.png
├── 04-elastic-ssh-login-event.png
├── 05-elastic-raw-auth-message.png
├── 06-elastic-authentication-failure.png
├── 07-elastic-ssh-session-start.png
└── 8-elastic-ssh-session-end.png
```

These screenshots demonstrate how the SSH activity was recorded at the Linux endpoint and ingested into Elastic.

### Evidence represented

* Linux authentication logs
* SSH journal records
* Authentication events
* SSH login events
* Authentication failure events
* SSH session start
* SSH session termination
* Raw authentication message

The telemetry evidence connects the attacker source:

```text
192.168.1.10
```

with:

```text
socadmin
soc-linux
sshd
```

---

# 03 — Hunting

```text
Hunting/
├── 01-valid-account-source-hunt.png
├── 02-ssh-authentication-chain-hunt.png
└── 03-post-ssh-activity-hunt.png
```

### 01-valid-account-source-hunt.png

KQL hunt correlating:

```text
host.name = soc-linux
user.name = socadmin
source.ip = 192.168.1.10
```

Time window:

```text
2026-10-02 08:36:00 → 08:37:00 IST
```

Purpose:

* Correlate user, source IP, and endpoint.
* Identify activity associated with the valid account.

### 02-ssh-authentication-chain-hunt.png

KQL hunt focused on:

```text
host.name = soc-linux
process.name = sshd
source.ip = 192.168.1.10
```

Purpose:

* Correlate SSH process activity with the attacker source.
* Identify authentication-related SSH events.

### 03-post-ssh-activity-hunt.png

KQL hunt focused on:

```text
host.name = soc-linux
user.name = socadmin
process.name = sshd
```

Purpose:

* Review SSH-related activity for the affected account.
* Correlate login and session-related events.

---

# 04 — Detection

```text
Detection/
├── 01-elastic-valid-account-alert.png
├── 02-detection-rule-overview.png
└── 03-detection-alert-generated.png
```

### 01-elastic-valid-account-alert.png

Shows the Elastic alert generated for SSH activity.

### 02-detection-rule-overview.png

Shows the detection rule configuration:

```text
Rule:
CatchMe - Linux SSH Valid Account Activity

Type:
Query

Severity:
Medium

Risk Score:
47
```

### 03-detection-alert-generated.png

Shows the generated detection alert and associated event context.

The alert was generated during Project 02 detection validation.

---

# 05 — Investigation

```text
Investigation/
├── 01-alert-investigation-overview.png
├── 02 -original-ssh-login-event.png
├── 03 -authentication-event-correlation.png
├── 04 -ssh-session-activity.png
└── 05 -investigation-source-user-correlation.png
```

### 01-alert-investigation-overview.png

Shows the investigation view of the generated SSH-related alert.

### 02 -original-ssh-login-event.png

Shows the original SSH login event associated with the investigation.

### 03 -authentication-event-correlation.png

Shows correlation between authentication-related events.

### 04 -ssh-session-activity.png

Shows SSH session activity associated with the affected account.

### 05 -investigation-source-user-correlation.png

Shows correlation between:

```text
Source IP
User
Host
SSH process
```

The investigation uses the actual Project 02 telemetry rather than simulated alert data.

---

# 06 — Incident Response

```text
Incident-Response/
├── 01-active-ssh-session-before-containment.png
├── 02-active-ssh-session-after-containment.png
├── 03-remediation-ssh-configuration-baseline.png
├── 04-remediation-account-privileges-baseline.png
├── 05-remediation-network-firewall-baseline.png
├── 06-remediation-ssh-key-authentication-success.png
├── 07-remediation-password-authentication-disabled.png
├── 08-remediation-ssh-reload-success.png
├── 09-remediation-password-authentication-blocked.png
├── 10-remediation-key-authentication-verified.png
├── 11-remediation-x11-forwarding-disabled.png
├── 12-remediation-x11-forwarding-reload-verified.png
├── 13-remediation-ufw-ssh-rule-added.png
├── 14-remediation-ufw-rule-verified.png
├── 15-remediation-ufw-enabled.png
├── 16-remediation-ssh-after-ufw-verified.png
└── 17-remediation-final-state.png
```

These screenshots document the containment, hardening, validation, and final-state verification performed on `soc-linux`.

### Configuration changes demonstrated

* SSH password authentication disabled.
* SSH public-key authentication retained.
* X11 forwarding disabled.
* SSH configuration syntax validated.
* SSH service reloaded successfully.
* UFW enabled.
* Incoming traffic restricted by firewall policy.
* SSH access permitted from the lab subnet.
* Public-key SSH authentication verified after remediation.
* Password-based SSH authentication tested and blocked.
* Final SSH and firewall state verified.

The remediation preserved key-based SSH access for the `socadmin` account.

---

# 07 — MITRE ATT&CK

```text
Mitre/
├── 01-T1078-valid-accounts.png
├── 02-T1021.004-ssh.png
└── 03-T1059.004-unix-shell.png
```

### 01-T1078-valid-accounts.png

Maps the observed valid-account activity to:

```text
T1078 — Valid Accounts
```

### 02-T1021.004-ssh.png

Maps the SSH remote-access activity to:

```text
T1021.004 — Remote Services: SSH
```

### 03-T1059.004-unix-shell.png

Documents the Unix shell technique where supported by the observed post-authentication activity.

---

# Evidence Handling

The screenshots are supporting visual evidence for the raw logs, Elastic events, detection results, investigation findings, and remediation records stored elsewhere in the project.

Screenshots should not be treated as a replacement for raw evidence.

The project maintains separate locations for:

```text
10-Queries/
11-Evidence/
09-Screenshots/
```

This separation keeps:

* queries
* raw evidence
* screenshots
* investigation records

organized independently.

---

# Screenshot Count

| Category          |  Count |
| ----------------- | -----: |
| Attack            |      2 |
| Telemetry         |      8 |
| Hunting           |      3 |
| Detection         |      3 |
| Investigation     |      5 |
| Incident-Response |     17 |
| MITRE             |      3 |
| **Total**         | **41** |

---

# Evidence Integrity

The screenshots document activity from the controlled CatchMe Linux SOC laboratory.

Fixed lab mapping:

| System         | Role                        | IP             |
| -------------- | --------------------------- | -------------- |
| `elastic-siem` | Elastic SIEM / Fleet Server | `192.168.1.11` |
| `soc-linux`    | Linux monitored endpoint    | `192.168.1.16` |
| `kali`         | Attack simulation system    | `192.168.1.10` |

Project 02 focuses on:

```text
Valid Account Abuse
        ↓
SSH Access
        ↓
Linux Telemetry
        ↓
Elastic Ingestion
        ↓
Threat Hunting
        ↓
Detection
        ↓
Investigation
        ↓
Containment
        ↓
SSH Hardening
        ↓
Validation
```

---

# Important Evidence Note

The Project 02 investigation established successful SSH authentication using the `socadmin` account from the controlled Kali system.

The observed telemetry also contains authentication-failure and session-related events.

Events must be interpreted using the corresponding Linux SSH journal and raw authentication evidence. In particular, Elastic `ssh_login`/`logged-in` event records must not automatically be interpreted as separate successful SSH authentications without correlation to the underlying Linux authentication logs.

No screenshot in this directory should be interpreted as evidence of privilege escalation, persistence, credential theft beyond the controlled credential-discovery simulation, data exfiltration, or destructive activity unless separately supported by project evidence.


