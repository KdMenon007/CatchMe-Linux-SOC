# Investigation Checklist — Project 04

## Evidence preservation
- [ ] Preserve `/opt/catchme-p04-lab/validation.log` on the target.
- [ ] Save the original Task 8 Elastic screenshot and exact query.
- [ ] Export relevant source event documents with document IDs and timestamps.
- [ ] Hash exported evidence and record the hash in `11-Evidence/Hashes/`.
- [ ] Keep sanitized copies separate from originals.

## Task 8 closure
- [x] Confirm `/etc/cron.d/catchme-p04-validation` is absent (target-side check, 2026-10-14).
- [x] Confirm `/opt/catchme-p04-lab/p04-cron-validation.sh` is absent (target-side check, 2026-10-14).
- [x] Check cron/systemd locations for references to the temporary script; none were found in the checked locations.
- [x] Confirm the validation log is preserved.
- [x] Record cleanup verification date and commands. Exact verification time was not recorded.

## Detection distinction
- [ ] Source process telemetry located.
- [ ] Process ancestry validated using IDs/parent IDs where available.
- [x] Alert status explicitly recorded as not confirmed; no actual alert record is claimed.
- [ ] Rule metadata/query retained if a rule was tested.

## Closure
- [ ] Findings and limitations reviewed.
- [ ] No unsupported claims of exploitation or compromise.
- [ ] Follow-up work assigned for tests requiring a disposable VM.
