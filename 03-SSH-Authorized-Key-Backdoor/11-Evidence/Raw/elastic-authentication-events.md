# Elastic Authentication Events — Evidence Capture Template

**Status:** Template only; no event documents are fabricated here.

For each actual Elastic event export, retain:
- `@timestamp` and timezone
- `host.name`, dataset, and event action
- Username and source IP when populated
- Authentication method and outcome when present
- Relevant event/document ID
- Query, data view, and selected time range
- Export or screenshot filename and hash reference

Preserve null fields as observed. Do not turn an absent field into an assumed value.
