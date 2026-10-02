# Remediation

## Project

**CatchMe Linux SOC — Project 02: Valid Account → SSH Hijacking**

## Remediation Objective

The remediation phase was performed after investigation of valid-account SSH activity involving the `socadmin` account and attacker source `192.168.1.10`.

The objective was to reduce SSH attack surface while preserving legitimate administrative access.

The remediation was performed directly on:

- **Linux Endpoint:** `soc-linux`
- **IP:** `192.168.1.16`
- **SSH Account:** `socadmin`
- **Attacker/Lab Host:** `192.168.1.10`
- **SSH Port:** `22/tcp`

---

## 1. Pre-Remediation Findings

The baseline showed the following SSH configuration:

| Control | Pre-Remediation State |
|---|---|
| PasswordAuthentication | `yes` |
| PubkeyAuthentication | `yes` |
| PermitRootLogin | `without-password` |
| MaxAuthTries | `6` |
| LoginGraceTime | `120` |
| X11Forwarding | `yes` |
| PermitUserEnvironment | `no` |
| UFW | `inactive` |
| SSH listener | `0.0.0.0:22` and `[::]:22` |
| `/home/socadmin/.ssh` | `0700` |
| `authorized_keys` | Empty at baseline |
| `socadmin` sudo access | `(ALL : ALL) ALL` |

### Baseline Evidence

- `09-Screenshots/Investigation/08-remediation-ssh-configuration-baseline.png`
- `09-Screenshots/Investigation/09-remediation-account-privileges-baseline.png`
- `09-Screenshots/Investigation/10-remediation-network-firewall-baseline.png`

---

# 2. SSH Key-Based Authentication

Before disabling password authentication, a dedicated Project 02 Ed25519 SSH key was created on Kali.

The public key was installed for `socadmin` on `soc-linux`.

The installation returned:

```text
Number of key(s) added: 1
```

Key-based authentication was then independently tested with password authentication explicitly disabled on the client.

Successful validation:

```text
KEY_AUTH_SUCCESS
socadmin
soc-linux
2026-10-02 11:09:06 IST
```

### Evidence

`09-Screenshots/Investigation/11-remediation-ssh-key-authentication-success.png`

---

# 3. Disable SSH Password Authentication

A backup of the original SSH configuration was created:

```text
/etc/ssh/sshd_config.project02-pre-remediation.bak
```

A Project 02 SSH hardening configuration was created:

```text
/etc/ssh/sshd_config.d/99-catchme-project02.conf
```

The effective SSH configuration initially remained:

```text
passwordauthentication yes
```

Investigation of the SSH configuration identified:

```text
/etc/ssh/sshd_config:12: Include /etc/ssh/sshd_config.d/*.conf
/etc/ssh/sshd_config.d/50-cloud-init.conf:1:PasswordAuthentication yes
/etc/ssh/sshd_config.d/99-catchme-project02.conf:1:PasswordAuthentication no
```

Because the earlier applicable setting was taking effect, `PasswordAuthentication no` was placed before the include statement in `/etc/ssh/sshd_config`.

The configuration was then syntax-tested successfully.

Effective configuration:

```text
passwordauthentication no
```

### Evidence

`09-Screenshots/Investigation/12-remediation-password-authentication-disabled.png`

---

# 4. Reload SSH Service

The SSH service was reloaded without terminating the existing administrative session.

Validation returned:

```text
active
pubkeyauthentication yes
passwordauthentication no
```

This confirmed that:

* SSH remained operational.
* Public-key authentication remained enabled.
* Password authentication was disabled.

### Evidence

`09-Screenshots/Investigation/13-remediation-ssh-reload-success.png`

---

# 5. Validate Password Authentication Blocking

From Kali, password authentication was explicitly requested while public-key authentication was disabled for the test.

The server rejected the connection with:

```text
Permission denied (publickey).
```

This demonstrated that password authentication was no longer accepted and that public-key authentication was the available authentication mechanism.

### Evidence

`09-Screenshots/Investigation/14-remediation-password-authentication-blocked.png`

---

# 6. Validate Key-Based Authentication After Hardening

After password authentication was disabled, the dedicated Project 02 SSH key was tested again.

The key-based authentication remained functional.

This verified that SSH hardening did not remove the legitimate administrative access path.

### Evidence

`09-Screenshots/Investigation/15-remediation-key-authentication-verified.png`

---

# 7. Disable X11 Forwarding

The baseline showed:

```text
x11forwarding yes
```

X11 forwarding was not required for the Project 02 SSH workflow.

The following SSH hardening setting was added:

```text
X11Forwarding no
```

The configuration passed `sshd -t` validation and the effective configuration became:

```text
x11forwarding no
```

### Evidence

`09-Screenshots/Investigation/16-remediation-x11-forwarding-disabled.png`

---

# 8. Reload SSH After X11 Hardening

The SSH service was reloaded successfully.

Validation returned:

```text
active
x11forwarding no
```

### Evidence

`09-Screenshots/Investigation/17-remediation-x11-forwarding-reload-verified.png`

---

# 9. UFW Firewall Configuration

The baseline showed:

```text
Status: inactive
```

Before enabling UFW, an SSH allow rule was created for the isolated lab network:

```text
ufw allow from 192.168.1.0/24 to any port 22 proto tcp
```

The rule was verified before enabling the firewall.

### Evidence

`09-Screenshots/Investigation/18-remediation-ufw-ssh-rule-added.png`

`09-Screenshots/Investigation/19-remediation-ufw-rule-verified.png`

---

# 10. Enable UFW

The firewall policy was configured as:

```text
Default: deny (incoming)
Default: allow (outgoing)
```

UFW was enabled successfully.

The resulting SSH rule was:

```text
22/tcp    ALLOW IN    192.168.1.0/24
```

UFW status became:

```text
Status: active
```

Logging was enabled at the low level.

### Evidence

`09-Screenshots/Investigation/20-remediation-ufw-enabled.png`

---

# 11. Validate SSH After Firewall Activation

After enabling UFW, SSH access using the dedicated Project 02 public key was tested from Kali.

Validation returned:

```text
FIREWALL_SSH_TEST_SUCCESS
socadmin
soc-linux
2026-10-02 11:22:15 IST
```

This confirmed that the firewall did not interrupt the legitimate SSH management path.

### Evidence

`09-Screenshots/Investigation/21-remediation-ssh-after-ufw-verified.png`

---

# 12. Final Remediation Verification

The final endpoint state was checked after all remediation changes.

Effective SSH configuration:

```text
logingracetime 120
maxauthtries 6
permitrootlogin without-password
pubkeyauthentication yes
passwordauthentication no
x11forwarding no
permituserenvironment no
```

SSH service:

```text
active
```

UFW:

```text
Status: active
Default: deny (incoming), allow (outgoing)
```

SSH firewall rule:

```text
22/tcp    ALLOW IN    192.168.1.0/24
```

SSH remained bound to:

```text
0.0.0.0:22
[::]:22
```

The `socadmin` SSH directory retained:

```text
drwx------ socadmin:socadmin /home/socadmin/.ssh
-rw------- socadmin:socadmin /home/socadmin/.ssh/authorized_keys
```

### Final Evidence

`09-Screenshots/Investigation/22-remediation-final-state.png`

---

# 13. Remediation Summary

| Security Control              | Before              | After                   | Validation                         |
| ----------------------------- | ------------------- | ----------------------- | ---------------------------------- |
| SSH password authentication   | Enabled             | **Disabled**            | Password login rejected            |
| SSH public-key authentication | Enabled             | **Enabled**             | Key login successful               |
| X11 forwarding                | Enabled             | **Disabled**            | `x11forwarding no`                 |
| UFW                           | Inactive            | **Active**              | Firewall status verified           |
| Incoming firewall policy      | Not enforced        | **Deny**                | UFW verified                       |
| SSH firewall access           | Unrestricted by UFW | **192.168.1.0/24 only** | Rule verified                      |
| SSH service                   | Active              | **Active**              | Service verified                   |
| SSH management access         | Password available  | **Key-based**           | Post-hardening SSH test successful |

---

# 14. Security Impact

The remediation reduced the SSH attack surface by:

1. Removing password-based SSH authentication.
2. Requiring the configured public-key authentication path.
3. Disabling unnecessary X11 forwarding.
4. Enabling host-based firewall enforcement.
5. Restricting inbound SSH access to the controlled lab network.
6. Preserving legitimate administrative access through the dedicated SSH key.

The remediation did **not** demonstrate or claim that all possible SSH attack paths were eliminated.

---

# 15. Important Remaining Configuration

The following setting remained unchanged:

```text
permitrootlogin without-password
```

This was not modified during this remediation stage.

The `socadmin` account also retains full sudo privileges:

```text
(ALL : ALL) ALL
```

These settings are documented as part of the final security state rather than being represented as remediation changes that were not actually performed.

---

# 16. Evidence Handling

All remediation screenshots were captured from the actual lab environment during the remediation process.

No fabricated configuration states or validation results were used.

The remediation evidence is stored under:

```text
09-Screenshots/Investigation/
```

Relevant remediation artifacts include:

```text
08-remediation-ssh-configuration-baseline.png
09-remediation-account-privileges-baseline.png
10-remediation-network-firewall-baseline.png
11-remediation-ssh-key-authentication-success.png
12-remediation-password-authentication-disabled.png
13-remediation-ssh-reload-success.png
14-remediation-password-authentication-blocked.png
15-remediation-key-authentication-verified.png
16-remediation-x11-forwarding-disabled.png
17-remediation-x11-forwarding-reload-verified.png
18-remediation-ufw-ssh-rule-added.png
19-remediation-ufw-rule-verified.png
20-remediation-ufw-enabled.png
21-remediation-ssh-after-ufw-verified.png
22-remediation-final-state.png
```

---

# 17. Remediation Conclusion

Project 02 remediation successfully changed the SSH authentication model from password-based access to validated public-key authentication, disabled X11 forwarding, and enabled an inbound-deny firewall policy with SSH permitted only from the controlled `192.168.1.0/24` laboratory network.

Post-remediation testing confirmed that:

* Password authentication was rejected.
* Public-key authentication remained functional.
* SSH remained active.
* X11 forwarding was disabled.
* UFW was active.
* SSH remained reachable from the authorized laboratory network.

The endpoint therefore remained operational while the documented SSH attack surface was reduced.


