# False-Positive Analysis — SSH Authorized-Key Detection

## Known observation
Earlier lab notes recorded five High alerts from a single controlled append-comment/restore operation. This indicates multiple alert records were produced around one controlled file operation; it does not establish whether those alerts are false positives.

## Questions to answer from actual alert documents
1. Do the alerts share an Auditd sequence or event identifier?
2. Which syscalls and timestamps are represented?
3. Is the same underlying operation represented by multiple record types?
4. Are target path and process fields populated?
5. Does rule scheduling or look-back explain repeated alert creation?
6. Is the activity expected lab validation, suspicious persistence, or indeterminate?

## Classification guidance
- **Expected validation activity:** only when the controlled test and matching event evidence are documented.
- **Duplicate/overlapping alert:** only when event IDs, timestamps, and rule execution context support that classification.
- **True positive:** only when evidence supports unauthorized or suspicious authorized-key modification.
- **Inconclusive:** use when the alert lacks enough event/path context.

## Current conclusion
No false-positive root cause is confirmed from the available summary alone. Inspect existing alerts before tuning the rule. Do not label all five alerts as false positives or as five separate incidents without evidence.
