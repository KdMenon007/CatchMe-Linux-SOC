# Linux SSH Journal Query Notes

**Purpose:** Document local journal review steps for SSH activity on `soc-linux`.

## Read-only commands

```bash
sudo journalctl -u ssh --since "2026-10-03 14:00:00" --until "2026-10-03 15:00:00" --output=short-iso
sudo journalctl _COMM=sshd --since "2026-10-03 14:00:00" --until "2026-10-03 15:00:00" --output=short-iso
```

Adjust the time range only when the investigation scope requires it. Confirm the host's timezone and actual unit name first.

## Evidence handling
- Save actual command output separately and record the collection time and host.
- Do not paste invented or reconstructed output into evidence.
- Redact secrets and unrelated personal data before public sharing.
- A missing journal entry does not establish that an event did not happen.
