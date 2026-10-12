# Detection Screenshot Evidence

## Existing screenshots

The following genuine lab screenshots were present in the project inventory when this README was updated:

- `P03-DET-05-authorized-keys-path-no-results.png` — A query for authorized-keys path activity returned no results in the documented historical search window. This is a scoped query result, not proof that no file modification occurred.
- `P03-DET-06-historical-syscall-sequence.png` — Historical Auditd syscall sequence reviewed during investigation. The indexed event fields did not establish the authorized-keys target path.
- `P03-DET-07-acknowledged-alerts-no-results.png` — Acknowledged alerts view with no results for October 3, 2026, 14:10–14:50 (lab-local time, IST).
- `P03-DET-08-closed-alerts-no-results.png` — Closed alerts view with no results for October 3, 2026, 14:10–14:50 (lab-local time, IST).

## Interpretation and limitations

- These screenshots document the views and results shown at capture time; filenames alone do not prove their contents.
- Empty acknowledged/closed alert views do not establish whether an alert was never generated, was in another status, fell outside the selected filters, or was otherwise unavailable.
- The historical detection outcome remains unresolved. Do not claim proven detection failure or successful end-to-end detection from these screenshots alone.
- The screenshots do not replace original event records, rule execution history, query text, time-range validation, or alert details where those are available.
- Review each image for sensitive information before public publication.

## Not present in the current inventory

The following names are examples of possible future evidence only; they are **not confirmed as existing files**:

- `P03-DET-01-rule-configuration.png`
- `P03-DET-02-alert-details.png`
- `P03-DET-03-alert-event-fields.png`
- `P03-DET-04-alert-timeline.png`

Capture genuine screenshots from the lab's Elastic Security interface if these views are needed. Do not create mock screenshots or imply that they exist before capture.

## Evidence integrity

The project-level screenshot SHA-256 manifest is stored at `../../11-Evidence/Hashes/screenshot-sha256-manifest.md`. Its 29 listed screenshot hashes were verified against the local files during the Phase 09 audit. Hash verification establishes file-byte integrity against that manifest, not screenshot meaning or provenance.
