# Containment, Eradication, and Recovery — Project 04

## Containment
For an actual unauthorized event, follow the organization's approved host-isolation process and preserve volatile evidence where feasible. Do not isolate the lab VM solely because the documented controlled test ran.

## Eradication / lab cleanup
For Task 8, target-side checks on 2026-10-14 confirmed that `/etc/cron.d/catchme-p04-validation` and `/opt/catchme-p04-lab/p04-cron-validation.sh` were absent. No references to the temporary script were found in the checked cron/systemd locations, and `/opt/catchme-p04-lab/validation.log` remained preserved. The exact verification time was not recorded.

Read-only verification:
```bash
sudo test ! -e /etc/cron.d/catchme-p04-validation \
  && echo "Temporary cron entry absent" \
  || echo "CHECK: temporary cron entry exists"

sudo test ! -e /opt/catchme-p04-lab/p04-cron-validation.sh \
  && echo "Temporary script absent" \
  || echo "CHECK: temporary script exists"

sudo grep -RFl -- '/opt/catchme-p04-lab/p04-cron-validation.sh' \
  /etc/cron.d /etc/crontab /etc/systemd/system 2>/dev/null
```

The grep command should produce no paths. If any check fails, investigate before changing state.

## Preserve evidence
Keep `/opt/catchme-p04-lab/validation.log`; it is part of the validation evidence. Do not delete the log or the lab directory during routine cleanup.

## Recovery
- Verify the original cron configuration and script permissions are restored.
- Confirm no temporary scheduled entry remains.
- Confirm Elastic Agent/Fleet and required telemetry are healthy.
- Record verification time and command output.
