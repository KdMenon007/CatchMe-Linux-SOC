# Remediation

## 1. Objective

The remediation phase applied security hardening changes to `soc-linux` following the Project 02 valid-account SSH activity.

The objective was to reduce the SSH attack surface, preserve controlled administrative access, and verify that the security controls remained functional after implementation.

---

## 2. Remediation Scope

| Item | Before Remediation | After Remediation |
|---|---|---|
| SSH Password Authentication | Enabled | Disabled |
| SSH Public-Key Authentication | Enabled | Enabled |
| X11 Forwarding | Enabled | Disabled |
| UFW | Inactive | Active |
| Incoming Firewall Policy | Not enforced | Deny |
| Outgoing Firewall Policy | Not enforced | Allow |
| SSH Firewall Source | Unrestricted by UFW | `192.168.1.0/24` |
| SSH Service | Active | Active |
| SSH Port | TCP/22 | TCP/22 |

---

## 3. SSH Configuration Remediation

A backup of the original SSH configuration was created before modifying the configuration:

```text
/etc/ssh/sshd_config.project02-pre-remediation.bak
```


Password authentication was disabled.

The effective SSH configuration was verified as:

```text
passwordauthentication no
pubkeyauthentication yes
```

The SSH configuration was syntax-validated and the SSH service was reloaded successfully.

Evidence:

* `03-remediation-ssh-configuration-baseline.png`
* `07-remediation-password-authentication-disabled.png`
* `08-remediation-ssh-reload-success.png`

---

## 4. Password Authentication Remediation Validation

A controlled connection from Kali attempted password authentication while public-key authentication was disabled.

The endpoint rejected password authentication and returned:

```text
Permission denied (publickey).
```

This confirmed that password-based SSH authentication was disabled at the effective SSH configuration level.

Evidence:

* `09-remediation-password-authentication-blocked.png`

---

## 5. Public-Key Authentication Validation

Because password authentication was disabled, controlled SSH administrative access was validated through public-key authentication.

The ED25519 key was installed for the `socadmin` account and successfully used for authentication.

Validation output:

```text
KEY_AUTH_SUCCESS
socadmin
soc-linux
2026-10-02 11:09:06 IST
```

This confirmed that the intended administrative access mechanism remained operational after disabling password authentication.

Evidence:

* `06-remediation-ssh-key-authentication-success.png`
* `10-remediation-key-authentication-verified.png`

---

## 6. X11 Forwarding Remediation

X11 forwarding was disabled to remove an unnecessary SSH feature from the controlled Linux endpoint.

The effective configuration was verified as:

```text
x11forwarding no
```

The SSH service was reloaded and the configuration was verified after reload.

Evidence:

* `11-remediation-x11-forwarding-disabled.png`
* `12-remediation-x11-forwarding-reload-verified.png`

---

## 7. Firewall Remediation

UFW was configured as the host firewall.

The SSH rule was restricted to the controlled laboratory subnet:

```text
22/tcp    ALLOW IN    192.168.1.0/24
```

The firewall default policies were configured as:

```text
Default: deny (incoming)
Default: allow (outgoing)
```

UFW was enabled successfully.

Evidence:

* `13-remediation-ufw-ssh-rule-added.png`
* `14-remediation-ufw-rule-verified.png`
* `15-remediation-ufw-enabled.png`

---

## 8. SSH Validation After Firewall Remediation

After enabling UFW, SSH public-key authentication was tested again from Kali.

The controlled validation returned:

```text
FIREWALL_SSH_TEST_SUCCESS
socadmin
soc-linux
2026-10-02 11:22:15 IST
```

This confirmed that the intended SSH management path remained available after firewall enforcement.

Evidence:

* `16-remediation-ssh-after-ufw-verified.png`

---

## 9. Final SSH Security Configuration

The final effective SSH configuration showed:

```text
loginGraceTime 120
maxAuthTries 6
permitRootLogin without-password
pubkeyAuthentication yes
passwordAuthentication no
x11Forwarding no
permitUserEnvironment no
```

The SSH service remained active.

Evidence:

* `17-remediation-final-state.png`

---

## 10. Remediation Validation

The remediation was validated through multiple independent checks:

| Control                   | Validation                | Result                    |
| ------------------------- | ------------------------- | ------------------------- |
| Password authentication   | Password-only SSH attempt | Blocked                   |
| Public-key authentication | ED25519 SSH connection    | Successful                |
| SSH service               | `systemctl is-active ssh` | Active                    |
| SSH configuration         | `sshd -T`                 | Expected settings applied |
| X11 forwarding            | `sshd -T`                 | Disabled                  |
| Firewall                  | `ufw status verbose`      | Active                    |
| Firewall SSH rule         | UFW rule inspection       | `192.168.1.0/24` allowed  |
| SSH after firewall        | Controlled SSH test       | Successful                |

---

## 11. Configuration Protection

The original SSH configuration was preserved before modification:

```text
/etc/ssh/sshd_config.project02-pre-remediation.bak
```

The new SSH hardening configuration was applied through:

```text
/etc/ssh/sshd_config.d/99-catchme-project02.conf
```

The configuration was syntax-validated before the SSH service was reloaded.

This provides a controlled rollback reference for the lab environment.

---

## 12. Remediation Evidence

| Screenshot                                            | Evidence                               |
| ----------------------------------------------------- | -------------------------------------- |
| `03-remediation-ssh-configuration-baseline.png`       | Original SSH security configuration    |
| `04-remediation-account-privileges-baseline.png`      | Account privilege baseline             |
| `05-remediation-network-firewall-baseline.png`        | Original firewall/network state        |
| `06-remediation-ssh-key-authentication-success.png`   | Public-key authentication setup        |
| `07-remediation-password-authentication-disabled.png` | Password authentication disabled       |
| `08-remediation-ssh-reload-success.png`               | SSH reload validation                  |
| `09-remediation-password-authentication-blocked.png`  | Password authentication blocked        |
| `10-remediation-key-authentication-verified.png`      | Public-key authentication verified     |
| `11-remediation-x11-forwarding-disabled.png`          | X11 forwarding disabled                |
| `12-remediation-x11-forwarding-reload-verified.png`   | X11 reload validation                  |
| `13-remediation-ufw-ssh-rule-added.png`               | SSH firewall rule added                |
| `14-remediation-ufw-rule-verified.png`                | Firewall rule verified                 |
| `15-remediation-ufw-enabled.png`                      | UFW enabled                            |
| `16-remediation-ssh-after-ufw-verified.png`           | SSH verified after firewall activation |
| `17-remediation-final-state.png`                      | Final security state                   |

---

## 13. Remediation Outcome

The remediation successfully changed the SSH security posture of `soc-linux`.

The final validated state:

* SSH password authentication disabled.
* SSH public-key authentication retained.
* X11 forwarding disabled.
* UFW enabled.
* Incoming traffic denied by default.
* SSH permitted from the controlled `192.168.1.0/24` laboratory subnet.
* SSH service remained operational.
* Public-key SSH access remained functional.
* Password-based SSH access was rejected.

**Remediation Status: COMPLETED**

