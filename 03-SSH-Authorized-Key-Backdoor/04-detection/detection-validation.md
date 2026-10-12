# Detection Validation — Evidence Checklist

## Current evidence status
- Prior notes record the rule configuration and five High alerts from one controlled append-comment/restore operation.
- The latest local inventory found no files in `09-Screenshots/Detection`.
- Project documentation explicitly says the historical successful SSH login is not proof that a detection alert fired.
- The current live rule state and alert contents were not independently checked by this documentation update.

## Validation checklist
- [ ] Open Elastic Security and confirm the rule exists and is enabled.
- [ ] Verify rule name, query, severity, risk score, ATT&CK mapping, schedule, and look-back.
- [ ] Inspect existing alerts in the relevant historical time range before generating any new activity.
- [ ] For a representative alert, record timestamp, rule name, host, event action, Auditd key, record type, syscall, sequence, and available path fields.
- [ ] Determine whether multiple alerts map to one underlying operation.
- [ ] Save genuine, sanitized screenshots under `09-Screenshots/Detection/`.
- [ ] Record query and time range used, plus no-result outcomes.
- [ ] Do not rerun attacks or change the live rule without a specific evidence-based reason and approval.

## Evidence naming suggestions
Use names such as:
- `P03-DET-01-rule-configuration.png`
- `P03-DET-02-alert-details.png`
- `P03-DET-03-alert-event-fields.png`
- `P03-DET-04-alert-timeline.png`

These are naming suggestions only; the files do not exist until genuine screenshots are captured.

## Outcome
Pending live rule and alert review. Do not mark detection validation as passed until the checklist is supported by actual evidence.

## Historical alert-status review — 2026-10-03

For 2026-10-03 14:10–14:50 (Asia/Kolkata), Elastic Security displayed no results in the Open, Acknowledged, and Closed alert views.

Recent rule executions succeeded with zero alerts created. This establishes only what was displayed in the selected time range and views; it does not prove that an alert was never generated outside that scope. Historical alert generation for the Oct 3 activity remains **not established**.

Evidence:
- `../09-Screenshots/Detection/P03-DET-05-authorized-keys-path-no-results.png`
- `../09-Screenshots/Detection/P03-DET-06-historical-syscall-sequence.png`
- `../09-Screenshots/Detection/P03-DET-07-acknowledged-alerts-no-results.png`
- `../09-Screenshots/Detection/P03-DET-08-closed-alerts-no-results.png`
