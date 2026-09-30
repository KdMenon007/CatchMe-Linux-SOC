# System Information Baseline

## Host Information

| Item | Value |
|---|---|
| Hostname | `soc-linux` |
| Endpoint IP | `192.168.1.16` |
| Operating System | Linux |
| Role | Linux SOC Monitoring Endpoint |

## System Baseline

The following system characteristics were collected during the initial baseline:

- Hostname
- Operating system
- Kernel version
- System architecture
- CPU configuration
- Memory allocation
- Disk and filesystem usage
- System uptime

## Security Monitoring Purpose

System information provides the foundation for identifying unexpected changes during later investigations.

Examples include:

- Unexpected kernel changes
- Unexpected system architecture changes
- Abnormal resource consumption
- Unexpected disk usage
- Unusual system uptime
- Changes to the operating system configuration

## Baseline Comparison

```text
Known System State
        ↓
New System Change
        ↓
Compare With Baseline
        ↓
Determine Whether Activity Is Expected
        ↓
Investigate If Suspicious
