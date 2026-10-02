# Project 02 — Containment and Remediation Evidence

## Evidence Type

Raw evidence documenting the SSH containment and remediation actions performed after the Project 02 investigation.

## Endpoint

- Hostname: `soc-linux`
- IP Address: `192.168.1.16`
- Account: `socadmin`
- Attacker/Lab Source: `192.168.1.10`

## SSH Authentication Remediation

A dedicated SSH configuration file was created:

```text
/etc/ssh/sshd_config.d/99-catchme-project02.conf
```

The effective SSH configuration was validated with:

```bash
sudo sshd -T | grep -E '^(passwordauthentication|pubkeyauthentication)'
```

Final effective configuration:

```text
passwordauthentication no
pubkeyauthentication yes
```

## SSH Configuration Validation

The SSH configuration was syntax-checked before reload:

```bash
sudo sshd -t
```

The SSH service was then reloaded and verified as active.

Final service state:

```text
active
```

## Password Authentication Test

A password-only SSH authentication test was performed:

```bash
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no -o NumberOfPasswordPrompts=1 socadmin@192.168.1.16
```

Observed result:

```text
Permission denied (publickey).
```

This confirmed that password-based SSH authentication was disabled.

## Public-Key Authentication

A dedicated project SSH key was used to maintain controlled administrative access.

Key:

```text
~/.ssh/catchme_project02_ed25519
```

Public-key authentication remained enabled after remediation.

## X11 Forwarding Remediation

The effective SSH configuration was changed to:

```text
x11forwarding no
```

The configuration was syntax-checked and the SSH service was reloaded successfully.

## Firewall Remediation

UFW was enabled with the following policy:

```text
Default: deny incoming
Default: allow outgoing
```

SSH access was explicitly permitted from the laboratory subnet:

```text
192.168.1.0/24 → TCP/22
```

UFW was verified as active after the configuration change.

## Firewall SSH Validation

SSH access from the controlled Kali laboratory host was tested after firewall activation.

Observed validation:

```text
FIREWALL_SSH_TEST_SUCCESS
socadmin
soc-linux
2026-10-02 11:22:15 IST
```

This confirmed that controlled SSH administrative access remained available from the laboratory subnet after firewall remediation.

## Final SSH Security State

| Control                       | Final State        |
| ----------------------------- | ------------------ |
| PasswordAuthentication        | Disabled           |
| PubkeyAuthentication          | Enabled            |
| X11Forwarding                 | Disabled           |
| PermitRootLogin               | `without-password` |
| SSH Service                   | Active             |
| UFW                           | Active             |
| UFW Incoming Policy           | Deny               |
| UFW Outgoing Policy           | Allow              |
| SSH Source Restriction        | `192.168.1.0/24`   |
| `authorized_keys` Permissions | `600`              |
| `.ssh` Directory Permissions  | `700`              |

## Evidence Screenshots

Related remediation evidence:

* `09-Screenshots/Incident-Response/03-remediation-ssh-configuration-baseline.png`
* `09-Screenshots/Incident-Response/04-remediation-account-privileges-baseline.png`
* `09-Screenshots/Incident-Response/05-remediation-network-firewall-baseline.png`
* `09-Screenshots/Incident-Response/06-remediation-ssh-key-authentication-success.png`
* `09-Screenshots/Incident-Response/07-remediation-password-authentication-disabled.png`
* `09-Screenshots/Incident-Response/08-remediation-ssh-reload-success.png`
* `09-Screenshots/Incident-Response/09-remediation-password-authentication-blocked.png`
* `09-Screenshots/Incident-Response/10-remediation-key-authentication-verified.png`
* `09-Screenshots/Incident-Response/11-remediation-x11-forwarding-disabled.png`
* `09-Screenshots/Incident-Response/12-remediation-x11-forwarding-reload-verified.png`
* `09-Screenshots/Incident-Response/13-remediation-ufw-ssh-rule-added.png`
* `09-Screenshots/Incident-Response/14-remediation-ufw-rule-verified.png`
* `09-Screenshots/Incident-Response/15-remediation-ufw-enabled.png`
* `09-Screenshots/Incident-Response/16-remediation-ssh-after-ufw-verified.png`
* `09-Screenshots/Incident-Response/17-remediation-final-state.png`

## Evidence Integrity Note

These results document the actual containment and remediation validation performed on `soc-linux`.

The remediation activity occurred after the attack and investigation and is therefore maintained separately from the original attack timeline.
