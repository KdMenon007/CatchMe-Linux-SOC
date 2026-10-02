# Project 02 — Containment

## Objective

Contain the observed SSH session associated with the Project 02 valid-account activity while preserving the active administrative investigation session.

The containment action was performed only within the controlled CatchMe laboratory environment.

---

## Incident Context

The investigation identified SSH activity involving:

| Field | Value |
|---|---|
| Source | Kali attacker |
| Source IP | `192.168.1.10` |
| Target | `soc-linux` |
| Target IP | `192.168.1.16` |
| Account | `socadmin` |
| Service | SSH |
| Process | `sshd` |

The primary confirmed successful authentication occurred at:

```text
2026-10-02 08:36:22.295 IST
```

---

## Pre-Containment State

Before containment verification, the endpoint showed an SSH connection from the attacker system:

```text
192.168.1.16:22 ← 192.168.1.10:35992
```

Associated SSH process:

```text
PID 2006
sshd: socadmin@pts/1
```

The endpoint also showed SSH sessions associated with the `socadmin` account.

### Pre-Containment Evidence

```text
09-Screenshots/Investigation/06-active-ssh-session-before-containment.png
```

This screenshot documents the SSH session state before the containment action.

---

## Containment Action

The older SSH session associated with the `pts/1` session was terminated.

The active investigation session was intentionally preserved.

The containment action was performed against the controlled laboratory environment only.

---

## Post-Containment Verification

After the containment action, the previous SSH connection:

```text
192.168.1.10:35992
```

was no longer present.

The previous SSH process:

```text
PID 2006
sshd: socadmin@pts/1
```

was also no longer present.

The remaining active SSH connection was:

```text
192.168.1.10:54248
        ↓
192.168.1.16:22
```

Associated processes:

```text
PID 2655
sshd: socadmin [priv]

PID 2731
sshd: socadmin@pts/0
```

This represents the remaining active investigation session.

### Post-Containment Evidence

```text
09-Screenshots/Investigation/07-active-ssh-session-after-containment.png
```

---

## Firewall State

The endpoint firewall was checked during containment validation.

Observed state:

```text
Status: inactive
```

No firewall rule was added as part of this containment action.

Therefore, the project does **not** claim that the source IP was blocked at the network firewall.

---

## Account State

The `socadmin` account was not disabled during this laboratory containment action.

No password rotation was performed as part of this stage.

No SSH authorized-key modification was performed.

---

## Containment Result

| Containment Item                        | Result        |
| --------------------------------------- | ------------- |
| Previous SSH connection terminated      | Confirmed     |
| Previous SSH process terminated         | Confirmed     |
| Current investigation session preserved | Confirmed     |
| Source IP firewall block                | Not performed |
| UFW enabled                             | No            |
| Account disabled                        | No            |
| Password changed                        | No            |
| Authorized keys modified                | No            |
| Host isolated                           | No            |

---

## Evidence Correlation

```text
Pre-Containment SSH Session
        |
        v
192.168.1.10 → 192.168.1.16:22
        |
        v
Identify SSH Process
        |
        v
Terminate Previous Session
        |
        v
Verify Process State
        |
        v
Verify Network State
        |
        v
Previous Connection Gone
        |
        v
Investigation Session Preserved
```

---

## Evidence Files

```text
09-Screenshots/Investigation/
├── 06-active-ssh-session-before-containment.png
└── 07-active-ssh-session-after-containment.png
```

---

## Containment Conclusion

The identified previous SSH session was successfully terminated in the controlled laboratory environment.

The active investigation connection was preserved to continue SOC validation.

No firewall block, account disablement, password rotation, host isolation, or SSH key modification was performed.

The containment result is therefore limited to **termination of the identified SSH session and verification that the associated previous SSH process and network connection were no longer active**.

````

