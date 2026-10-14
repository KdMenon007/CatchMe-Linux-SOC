# Sigma Rules — Project 04

## Current candidate rule

- `linux-sudo-authorization-denial.yml` — candidate detection for Linux sudo authorization-denial messages.

## Validation status

The YAML syntax has been parsed successfully with PyYAML. This does not establish Sigma compatibility, correct Elastic field mapping, a matching event, or an alert.

Before marking this rule validated:

- [ ] Validate with a compatible Sigma tool.
- [ ] Confirm the actual authentication event and its field names in Elastic Discover.
- [ ] Test the KQL query against the same host and time range.
- [ ] Record whether matching source events exist.
- [ ] Test benign authorization denials and document false positives.
- [ ] Capture an actual alert record before claiming alert generation.

The controlled Task 8 cron execution is a separate investigation scenario. This sudo-denial rule does not detect that cron activity.
