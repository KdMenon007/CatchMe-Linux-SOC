# Project 02 — Sanitized Remediation Evidence

## Purpose

Document the verified SSH containment and remediation state after the Project 02 investigation.

## Endpoint

| Field | Value |
|---|---|
| Host | `soc-linux` |
| IP | `192.168.1.16` |
| Administrative account | `socadmin` |
| Laboratory source | `192.168.1.10` |

## SSH Hardening

Final effective SSH configuration:

```text
permitrootlogin without-password
pubkeyauthentication yes
passwordauthentication no
x11forwarding no
permituserenvironment no
```

## Authentication Control

Password-based SSH authentication was disabled.

Public-key authentication remained enabled for controlled administrative access.

Password-only authentication testing returned:

```text
Permission denied (publickey).
```

This verified that password authentication was no longer accepted.

## SSH Service

The SSH configuration was syntax-validated before reload.

The SSH service remained active after the configuration changes.

## X11 Forwarding

X11 forwarding was disabled:

```text
x11forwarding no
```

The SSH configuration was validated and the service successfully reloaded.

## Firewall Hardening

UFW was enabled with:

```text
Default: deny incoming
Default: allow outgoing
```

SSH access was permitted from the laboratory subnet:

```text
192.168.1.0/24 → TCP/22
```

## Firewall Validation

Controlled SSH access from the Kali laboratory host remained functional after UFW activation.

Observed validation:

```text
FIREWALL_SSH_TEST_SUCCESS
socadmin
soc-linux
2026-10-02 11:22:15 IST
```

## SSH Key Security

The controlled administrative SSH key remained available for public-key authentication.

The SSH directory and authorized-key file retained restricted permissions:

```text
.ssh                  700
authorized_keys       600
```

## Final Security State

| Control                   | Final State        |
| ------------------------- | ------------------ |
| SSH service               | Active             |
| Password authentication   | Disabled           |
| Public-key authentication | Enabled            |
| Root password SSH access  | Disabled           |
| X11 forwarding            | Disabled           |
| User environment          | Disabled           |
| UFW                       | Active             |
| Incoming traffic          | Denied by default  |
| Outgoing traffic          | Allowed by default |
| SSH allowed source        | `192.168.1.0/24`   |

## Related Screenshots

* `09-Screenshots/Incident-Response/07-remediation-password-authentication-disabled.png`
* `09-Screenshots/Incident-Response/09-remediation-password-authentication-blocked.png`
* `09-Screenshots/Incident-Response/10-remediation-key-authentication-verified.png`
* `09-Screenshots/Incident-Response/11-remediation-x11-forwarding-disabled.png`
* `09-Screenshots/Incident-Response/15-remediation-ufw-enabled.png`
* `09-Screenshots/Incident-Response/16-remediation-ssh-after-ufw-verified.png`
* `09-Screenshots/Incident-Response/17-remediation-final-state.png`

## Sanitization Controls

This public-facing evidence excludes:

* Passwords
* Private SSH keys
* Credential wordlists
* Authentication secrets
* Unnecessary private system configuration

## Evidence Integrity

This document records the verified remediation state after the Project 02 investigation.

Containment and remediation evidence is kept separate from the original attack timeline and detection-validation activity.

