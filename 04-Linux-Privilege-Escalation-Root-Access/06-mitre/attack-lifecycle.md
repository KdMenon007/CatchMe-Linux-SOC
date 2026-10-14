# ATT&CK Lifecycle Context — Project 04

This project focuses on Linux privilege-escalation discovery and validation. The evidence currently supports one controlled scheduled-task demonstration and multiple baseline inspections.

## Evidence-led sequence
1. **Discovery / enumeration:** Commands such as `stat`, `find`, `getcap`, and service/configuration inspection were observed during baseline checks. These are dual-use administrative activities and should not be treated as malicious in isolation.
2. **Scheduled execution:** Task 8 used a temporary cron entry to run a lab validation script in root context. T1053.003 is a candidate mapping for this controlled behavior.
3. **Privilege outcome:** The host-side validation log reportedly recorded UID 0. Preserve the log and correlate it with original Elastic event documents.
4. **Detection:** No dedicated Elastic alert is confirmed in the available project record.
5. **Cleanup:** The temporary cron entry was reported absent. Final script absence must be verified before marking cleanup complete.

## Exclusions
Do not infer persistence, credential theft, defense evasion, lateral movement, or real adversary activity from this lab exercise.
