# Project 02 — Final Remediation State Evidence

## Evidence Type

Final-state evidence collected after containment and remediation of the Project 02 SSH valid-account activity.

## Endpoint

- Hostname: `soc-linux`
- IP Address: `192.168.1.16`
- Account: `socadmin`
- Attacker/Lab Source: `192.168.1.10`

## Final SSH Configuration

The effective SSH configuration was verified after remediation.

```text
permitrootlogin without-password
pubkeyauthentication yes
passwordauthentication no
x11forwarding no
permituserenvironment no
```

## SSH Service

The SSH service was verified as active after configuration changes.

```text
active
```

## SSH Authentication Controls

Password authentication was disabled.

Public-key authentication remained enabled to preserve controlled administrative access.

The dedicated project SSH key successfully authenticated to the endpoint during remediation validation.

## SSH Account and Key Permissions

The `socadmin` SSH directory and authorized-key file retained restricted permissions:

```text
.ssh                  700
authorized_keys       600
```

The `authorized_keys` file remained owned by `socadmin`.

## Firewall State

UFW was enabled after remediation.

Final policy:

```text
Default: deny incoming
Default: allow outgoing
```

SSH was permitted from the laboratory subnet:

```text
192.168.1.0/24 → TCP/22
```

## Final SSH Connectivity Validation

Controlled SSH access from the Kali laboratory host was successfully validated after firewall activation.

Observed validation:

```text
FIREWALL_SSH_TEST_SUCCESS
socadmin
soc-linux
2026-10-02 11:22:15 IST
```

## Final Security State

| Control                          | Final State      |
| -------------------------------- | ---------------- |
| SSH service                      | Active           |
| Password authentication          | Disabled         |
| Public-key authentication        | Enabled          |
| Root password SSH authentication | Disabled         |
| X11 forwarding                   | Disabled         |
| User environment                 | Disabled         |
| UFW                              | Active           |
| Incoming firewall policy         | Deny             |
| Outgoing firewall policy         | Allow            |
| SSH allowed source               | `192.168.1.0/24` |
| `.ssh` permissions               | `700`            |
| `authorized_keys` permissions    | `600`            |

## Remediation Outcome

The final state demonstrates that:

1. Password-based SSH authentication was disabled.
2. Controlled public-key authentication remained available.
3. X11 forwarding was disabled.
4. The SSH service remained operational.
5. UFW was enabled with a restrictive inbound policy.
6. SSH access remained available from the defined laboratory subnet.
7. The endpoint remained accessible for controlled administrative validation.

## Related Screenshots

* `09-Screenshots/Incident-Response/10-remediation-key-authentication-verified.png`
* `09-Screenshots/Incident-Response/12-remediation-x11-forwarding-reload-verified.png`
* `09-Screenshots/Incident-Response/15-remediation-ufw-enabled.png`
* `09-Screenshots/Incident-Response/16-remediation-ssh-after-ufw-verified.png`
* `09-Screenshots/Incident-Response/17-remediation-final-state.png`

## Evidence Integrity Note

This document records the final verified endpoint state after Project 02 containment and remediation. It is separate from the original attack evidence and detection-validation timeline.
