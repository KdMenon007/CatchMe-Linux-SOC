# Investigation Query

**Purpose:** Correlate SSH authentication, authorized-key file activity, and related endpoint events.

## Candidate query

```kql
host.name:"soc-linux" AND (
  process.name:"sshd" OR
  file.path:"/home/socadmin/.ssh/authorized_keys" OR
  event.category:authentication
)
```

## Investigation steps
1. Select the evidence-backed time range and correct data view.
2. Run the query and inspect returned documents and field values.
3. Pivot using event timestamps, host, user, process identifiers, and source address where present.
4. Note missing or null fields explicitly; do not infer a target path from `log.file.path`.
5. Save the exact query and record hit count and supporting evidence references.

**Caveat:** Field availability and ECS mappings vary. Validate fields against actual documents before drawing conclusions.
