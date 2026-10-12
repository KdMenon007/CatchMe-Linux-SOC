# Authentication Failure Hunt

**Purpose:** Document the SSH authentication-failure hunt for Project 03.

## Query

```kql
host.name:"soc-linux" AND (event.action:"ssh_login_failure" OR message:*Failed*password*)
```

## Use and limitations
- Confirm the actual field names and values in the current Elastic data view before relying on this query.
- Use the relevant time range and compare timestamps with the investigation timeline.
- A query result supports only the events and fields actually returned; a no-result does not prove the event did not occur.
- Record the exact query, time range, hit count, and screenshot or export reference when executed.

**Evidence status:** This file documents a candidate query. It does not itself prove that authentication failures were found.
