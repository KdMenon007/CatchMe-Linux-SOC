# Hunting Methodology — SSH Authorized-Key Backdoor

## Method
1. Establish the fixed lab hosts and historical time window.
2. Start with broad host-and-dataset searches to confirm data availability.
3. Pivot from SSH authentication events to source IP, account, method, outcome, and session lifecycle.
4. Inspect Auditd record types and syscall records without assuming a syscall identifies a file.
5. Search candidate path fields for the authorized-key target.
6. Compare indexed evidence with endpoint journal and terminal screenshots.
7. Record positive, negative, and inconclusive searches with their exact scope.
8. State conclusions only at the level supported by the visible fields.

## Search window
Historical review used October 3, 2026, approximately 14:10–14:50 IST (`UTC+05:30`). Some focused searches used 14:35–14:47 IST.

## Observed methodology results
- Broad Auditd query returned 153 documents for 14:35–14:47 IST.
- `auditd.log.syscall:*` returned 12 documents, including `write` syscall records.
- Sequence `966` returned nine related documents at approximately 14:40:11.939.
- Searches for `auditd.log.name`, `auditd.log.path`, `file.path`, and `auditd.log.record_type:"PATH"` returned no results in the searched historical window.
- The public-key re-entry event is visible as an Elastic authentication event.

## Guardrails
- A no-results query is specific to its data view, field, syntax, and time range.
- Do not equate a `write` syscall with the authorized-key file write.
- Do not infer causality solely from nearby timestamps.
- Do not include passwords, key material, tokens, or unsanitized raw logs.
- No attack rerun or configuration change is part of this historical evidence review.
