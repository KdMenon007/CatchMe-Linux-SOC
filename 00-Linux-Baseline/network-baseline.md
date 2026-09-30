# Network Baseline

## Network Identity

| Item | Value |
|---|---|
| Hostname | `soc-linux` |
| Endpoint IP | `192.168.1.16` |
| SIEM Hostname | `elastic-siem` |
| SIEM IP | `192.168.1.11` |

## Network Configuration

The baseline records:

- Network interfaces
- Assigned IP addresses
- Default gateway
- Routing configuration
- DNS configuration
- TCP listening ports
- UDP listening ports
- Active network connections

## Network Monitoring Purpose

The network baseline provides a known-good reference for identifying unexpected network activity during later security investigations.

Examples include:

- Unexpected listening ports
- Unexpected services bound to network interfaces
- Unusual outbound connections
- Unexpected remote hosts
- Changes to routing configuration
- Unexpected DNS configuration

## Listening Services

Listening ports identified during baseline collection should be documented and compared against later observations.

```text
Known Listening Port
        ↓
New or Unexpected Port
        ↓
Identify Process
        ↓
Investigate Service
        ↓
Determine Whether Activity Is Expected

## Active Connections

Active network connections provide additional context for future investigations.

During an investigation, connections can be correlated with:

* Source address
* Destination address
* Destination port
* Process
* User
* Timestamp
* Related authentication or system activity

## Baseline Comparison

```text
Normal Network State
        ↓
Observed Network Change
        ↓
Compare With Baseline
        ↓
Correlate With Process and User
        ↓
Investigate If Suspicious
```


