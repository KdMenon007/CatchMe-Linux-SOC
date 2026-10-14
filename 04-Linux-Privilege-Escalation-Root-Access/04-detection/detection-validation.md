# Detection Validation — Project 04

## Validation status
The current record supports a controlled Task 8 root-context execution demonstration and process telemetry review. It does **not** establish that a dedicated Elastic detection rule generated an alert.

## Validation procedure
1. Record rule name, rule ID, version, enabled status, and index/data view.
2. Record the exact KQL/EQL query and schedule/interval.
3. Select a time window containing the actual test events, in `Asia/Kolkata`.
4. Confirm the source events exist in Discover.
5. Run or wait for the rule according to its configured schedule.
6. Inspect the Alerts view for a matching alert document.
7. Save the alert ID, rule metadata, event timestamp, host, user, process chain, and screenshot.
8. If no alert exists, record whether the rule did not match, was not scheduled, or the data/fields were unavailable.
9. Clean up the test and verify final state.

## Task 8 review window
The previously used Elastic review window was approximately **2026-10-13 12:37–12:49 IST**. Use it only for the Task 8 event review; do not apply it to later baseline tasks.

## Acceptance criteria
- Source telemetry: present and attributable.
- Alert: documented only if an actual alert record is present.
- Detection logic: query and rule metadata preserved.
- Cleanup: temporary cron entry absent; verify script absence before marking cleanup complete.

## Sudo-denial query validation — 2026-10-14

- **Host:** `soc-linux`
- **Dataset:** `system.auth`
- **Process:** `sudo`
- **Observed messages:** `user NOT in sudoers`; `command not allowed`
- **Query:** `10-Queries/KQL/P04-Sudo-Denial.kql`
- **Result:** Matching source documents were visible in Elastic Discover.
- **Alert status:** Not verified; no Elastic Security alert record was captured as part of this validation.
- **Duplicate-document review:** Pending. Multiple documents shared displayed timestamps and messages; source offsets and ingestion metadata require comparison before drawing a conclusion.
