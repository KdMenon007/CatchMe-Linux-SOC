# Containment

## 1. Objective

The containment phase was performed after the Project 02 investigation identified valid-account SSH activity involving the `socadmin` account on the Linux endpoint.

The objective was to reduce the available SSH attack surface while preserving controlled administrative access through SSH public-key authentication.

---

## 2. Containment Scope

| Item | Value |
|---|---|
| Target Host | `soc-linux` |
| Target IP | `192.168.1.16` |
| Attacker Host | Kali |
| Attacker IP | `192.168.1.10` |
| Account Observed | `socadmin` |
| Service | SSH |
| Port | TCP/22 |
| Password Authentication Before Containment | Enabled |
| Public-Key Authentication | Enabled |
| Password Authentication After Containment | Disabled |
| Public-Key Authentication After Containment | Enabled |

---

## 3. Pre-Containment State

The pre-containment SSH configuration showed:

```text
loginGraceTime 120
maxAuthTries 6
permitRootLogin without-password
pubkeyAuthentication yes
passwordAuthentication yes
x11Forwarding yes
permitUserEnvironment no
```

The `socadmin` account was a local account with sudo privileges.

The SSH service was listening on:

```text
0.0.0.0:22
[::]:22
```

The `/home/socadmin/.ssh` directory had restrictive permissions:

```text
drwx------ socadmin:socadmin
```

The `authorized_keys` file had:

```text
-rw------- socadmin:socadmin
```

UFW was initially inactive.

Evidence:

* `03-remediation-ssh-configuration-baseline.png`
* `04-remediation-account-privileges-baseline.png`
* `05-remediation-network-firewall-baseline.png`

---

## 4. Containment Actions

### 4.1 Disable SSH Password Authentication

SSH password authentication was disabled.

The effective configuration was verified as:

```text
passwordauthentication no
pubkeyauthentication yes
```

The SSH configuration was syntax-validated and the SSH service was reloaded.

Evidence:

* `07-remediation-password-authentication-disabled.png`
* `08-remediation-ssh-reload-success.png`

---

### 4.2 Validate Password Authentication Blocking

A controlled SSH connection from Kali attempted to use password authentication while public-key authentication was disabled.

The connection returned:

```text
Permission denied (publickey).
```

This demonstrated that password-based SSH authentication was no longer accepted.

Evidence:

* `09-remediation-password-authentication-blocked.png`

---

### 4.3 Preserve and Validate SSH Public-Key Authentication

An ED25519 SSH key was created for the controlled Project 02 validation.

The public key was installed for the `socadmin` account.

Key-based authentication was successfully validated:

```text
KEY_AUTH_SUCCESS
socadmin
soc-linux
2026-10-02 11:09:06 IST
```

This confirmed that disabling password authentication did not prevent the intended SSH administrative access.

Evidence:

* `06-remediation-ssh-key-authentication-success.png`
* `10-remediation-key-authentication-verified.png`

---

### 4.4 Disable X11 Forwarding

X11 forwarding was disabled.

The effective configuration was verified as:

```text
x11forwarding no
```

The SSH service was reloaded and the configuration was verified.

Evidence:

* `11-remediation-x11-forwarding-disabled.png`
* `12-remediation-x11-forwarding-reload-verified.png`

---

### 4.5 Enable and Restrict the Host Firewall

UFW was configured to deny incoming traffic by default and allow outgoing traffic.

The SSH rule was restricted to the controlled lab subnet:

```text
22/tcp    ALLOW IN    192.168.1.0/24
```

UFW was then enabled.

Final firewall state:

```text
Status: active
Default: deny (incoming), allow (outgoing)
```

Evidence:

* `13-remediation-ufw-ssh-rule-added.png`
* `14-remediation-ufw-rule-verified.png`
* `15-remediation-ufw-enabled.png`

---

## 5. Active SSH Session Validation

Before containment, an active SSH connection from Kali to `soc-linux` was observed.

The connection showed:

```text
192.168.1.16:22
192.168.1.10
```

The endpoint also showed an active `sshd` process associated with `socadmin`.

Post-containment validation confirmed that the intended SSH access path remained functional.

Evidence:

* `01-active-ssh-session-before-containment.png`
* `02-active-ssh-session-after-containment.png`

---

## 6. SSH Validation After Firewall Activation

After enabling UFW, SSH public-key authentication was tested again.

The controlled validation returned:

```text
FIREWALL_SSH_TEST_SUCCESS
socadmin
soc-linux
2026-10-02 11:22:15 IST
```

This confirmed that the firewall rule allowed the intended SSH management path from the lab subnet.

Evidence:

* `16-remediation-ssh-after-ufw-verified.png`

---

## 7. Final Containment State

The final effective SSH configuration showed:

```text
passwordauthentication no
pubkeyauthentication yes
x11forwarding no
```

The SSH service remained active.

UFW was active with:

```text
Default: deny (incoming)
Default: allow (outgoing)
```

The SSH firewall rule was:

```text
22/tcp    ALLOW IN    192.168.1.0/24
```

Evidence:

* `17-remediation-final-state.png`

---

## 8. Containment Effect

The containment changes reduced the SSH attack surface by:

1. Removing password-based SSH authentication.
2. Retaining controlled public-key authentication.
3. Disabling X11 forwarding.
4. Enabling the host firewall.
5. Restricting inbound SSH to the lab subnet.
6. Verifying that intended SSH access remained functional.

The `socadmin` account's existing sudo privileges were not changed during this containment phase.

---

## 9. Evidence Mapping

| Screenshot                                            | Evidence                               |
| ----------------------------------------------------- | -------------------------------------- |
| `01-active-ssh-session-before-containment.png`        | Active SSH session before containment  |
| `02-active-ssh-session-after-containment.png`         | SSH session state after containment    |
| `03-remediation-ssh-configuration-baseline.png`       | Pre-containment SSH configuration      |
| `04-remediation-account-privileges-baseline.png`      | `socadmin` privilege baseline          |
| `05-remediation-network-firewall-baseline.png`        | Network/firewall baseline              |
| `06-remediation-ssh-key-authentication-success.png`   | SSH public-key authentication setup    |
| `07-remediation-password-authentication-disabled.png` | Password authentication disabled       |
| `08-remediation-ssh-reload-success.png`               | SSH reload and validation              |
| `09-remediation-password-authentication-blocked.png`  | Password authentication blocked        |
| `10-remediation-key-authentication-verified.png`      | Public-key authentication verified     |
| `11-remediation-x11-forwarding-disabled.png`          | X11 forwarding disabled                |
| `12-remediation-x11-forwarding-reload-verified.png`   | X11 forwarding reload verified         |
| `13-remediation-ufw-ssh-rule-added.png`               | SSH firewall rule added                |
| `14-remediation-ufw-rule-verified.png`                | Firewall rule verified                 |
| `15-remediation-ufw-enabled.png`                      | UFW enabled                            |
| `16-remediation-ssh-after-ufw-verified.png`           | SSH verified after firewall activation |
| `17-remediation-final-state.png`                      | Final containment state                |

---

## 10. Containment Conclusion

Project 02 containment was completed on `soc-linux`.

The controlled response:

* Disabled SSH password authentication.
* Preserved SSH public-key authentication.
* Verified password-based SSH access was blocked.
* Verified public-key SSH access remained functional.
* Disabled X11 forwarding.
* Enabled UFW.
* Restricted SSH access to `192.168.1.0/24`.
* Verified SSH functionality after firewall activation.
* Preserved evidence for the containment actions and validation steps.

The endpoint remained operational and accessible through the intended controlled SSH management path.

**Containment Status: COMPLETED**


