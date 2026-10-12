# SSH Process Correlation

**Purpose:** Provide a starting point for correlating SSH daemon activity with authentication events.

## Candidate query

```kql
host.name:"soc-linux" AND process.name:"sshd"
```

## Correlation checklist
- Compare event timestamps using the same timezone.
- Correlate host, user, source IP, process PID, audit sequence, and session identifiers only when populated.
- Distinguish a source log path from the path of a file affected by an event.
- Record null fields and gaps; do not manufacture a process-to-file relationship when identifiers are absent.

This is a query starting point, not a verified event result.
