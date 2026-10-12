# Endpoint Validation Query

**Purpose:** Validate whether endpoint events for the lab host are present in the selected Elastic data view.

## Candidate KQL

```kql
host.name:"soc-linux"
```

Optionally narrow by a confirmed dataset, event category, or time range after checking the available fields.

## Record
- Endpoint name and expected time range
- Data view and dataset used
- Total hit count and relevant event types
- Missing sources or fields, with example document references
- Whether the endpoint was online and the agent was healthy during the period

A query returning no documents is a telemetry gap to investigate, not proof that no activity occurred.
