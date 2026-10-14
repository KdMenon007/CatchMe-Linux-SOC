# P04 — Sudo Authorization Denial Query

**Purpose:** Find sudo authorization-denial messages for `soc-linux`.

**Query:** `P04-Sudo-Denial.kql`

**Status:** Candidate query; not yet validated against the actual event fields.

**Important:** Matching text varies by Linux distribution and sudo configuration. Confirm the actual event in Elastic Discover and adjust the message patterns and data view if needed. A query match is source telemetry, not automatically a detection alert.
