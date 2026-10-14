# Detection Coverage Matrix — Project 04

| Activity | Expected source | Current evidence | Coverage statement |
|---|---|---|---|
| Cron validation execution | Process telemetry; host validation log | Task 8 process activity and root-context log reported | Controlled behavior supported |
| Cron PATH inspection | Process telemetry | Baseline check reported | No PATH hijack demonstrated |
| Cron wildcard inspection | Process telemetry | Baseline check reported | No wildcard escalation demonstrated |
| SUID/SGID inventory | Process/file metadata | Inventory and package checks reported | No exploit demonstrated |
| Shared-library inspection | Process and file metadata | Dependency and permission checks reported | No injection demonstrated |
| Environment/shell/capability checks | Process telemetry and metadata | Baseline checks reported | No shell-feature escalation demonstrated |
| History/configuration/SSH key checks | Process telemetry and file metadata | Permission metadata reported | No credential exposure demonstrated |
| NFS checks | Process/service/mount evidence | No mounts or active NFS services reported | No NFS exploit demonstrated |
| Kernel posture | OS/package/mitigation output | Version and mitigation status recorded | No kernel exploit demonstrated |

## Important distinction
“Checked” does not mean “fully covered.” File-change detection depends on the configured telemetry and field mappings. Confirm actual event documents before marking any source as complete.
