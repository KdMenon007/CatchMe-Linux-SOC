# Network Investigation

## Lab endpoints
- Kali / scenario source: `192.168.1.10`
- Linux endpoint: `soc-linux`, `192.168.1.16`
- Elastic SIEM: `elastic-siem`, `192.168.1.11`
- Gateway: `192.168.1.1`
- Lab subnet: `192.168.1.0/24`

## Observations
The successful public-key SSH event records source IP `192.168.1.10` and endpoint `soc-linux` (`192.168.1.16`). Existing screenshots document SSH activity between the lab hosts.

## Assessment and limits
The source/destination relationship is supported by the recorded SSH event and existing lab documentation. This does not establish a complete packet-level reconstruction, all network connections during the window, or external data transfer.

## Evidence references
- `../09-Screenshots/Telemetry/P03-TEL-01-ssh-password-attack.png`
- `../09-Screenshots/Telemetry/P03-TEL-02-ssh-initial-access.png`
- `../09-Screenshots/Telemetry/P03-TEL-05-key-based-reentry.png`
- `../09-Screenshots/Hunting/P03-HUNT-08-ssh-session-correlation.png`
- `../09-Screenshots/Hunting/P03-HUNT-08-ssh-session-lifecycle.png`
