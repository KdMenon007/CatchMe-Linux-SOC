# Network Baseline

## Network Identity

| Field | Value |
|---|---|
| Endpoint Hostname | `soc-linux` |
| Endpoint IP Address | `192.168.1.16` |
| Endpoint Role | Linux SOC Monitoring Endpoint |
| SIEM Hostname | `elastic-siem` |
| SIEM IP Address | `192.168.1.11` |
| SIEM Role | Elastic SIEM / Kibana / Fleet Server |
| Network Range | `192.168.1.0/24` |

## Network Architecture

```text
                    CatchMe Linux SOC

                 Local Network
                 192.168.1.0/24
                        |
            +-----------+-----------+
            |                       |
            v                       v
      elastic-siem              soc-linux
      192.168.1.11             192.168.1.16
            |                       |
            |                   Elastic Agent
            |                       |
            |                     Auditd
            |                       |
            +----------+------------+
                       |
                 Security Telemetry
```
## Network Components

| Component      | Hostname       | IP Address     | Role                                  |
| -------------- | -------------- | -------------- | ------------------------------------- |
| Elastic SIEM   | `elastic-siem` | `192.168.1.11` | Elasticsearch / Kibana / Fleet Server |
| Linux Endpoint | `soc-linux`    | `192.168.1.16` | Monitored Linux endpoint              |
| Fleet Server   | `elastic-siem` | `192.168.1.11` | Elastic Agent management              |

## Hostname Resolution

The Linux endpoint uses the Elastic SIEM hostname for Fleet communication.

Expected resolution:

```text
elastic-siem → 192.168.1.11
```

This hostname-to-IP mapping is an important part of the lab baseline.

Any unexpected change should be verified before modifying Elastic or Fleet configuration.

## Network Configuration

The baseline records:

* Network interfaces
* Assigned IP addresses
* Routing information
* DNS configuration
* Listening TCP ports
* Listening UDP ports
* Active network connections

## Network Collection Commands

### Network Interfaces

```bash
ip -br addr
```

Purpose:

* Identify active interfaces
* Identify assigned addresses
* Establish endpoint network identity

### Routing

```bash
ip route
```

Purpose:

* Identify connected networks
* Identify default gateway
* Establish normal routing state

### DNS

```bash
resolvectl status
```

Purpose:

* Identify DNS configuration
* Establish normal name-resolution state
* Support investigation of suspicious DNS activity

### Listening Services

```bash
sudo ss -tulpen
```

Purpose:

* Identify TCP listeners
* Identify UDP listeners
* Identify listening addresses
* Identify processes associated with listening ports

### Active Connections

```bash
sudo ss -tunap
```

Purpose:

* Identify established connections
* Identify local and remote endpoints
* Identify associated processes
* Establish normal network activity

## Expected Elastic Communication

The Linux endpoint communicates with Fleet Server using:

```text
https://elastic-siem:8220
```

Network flow:

```text
soc-linux
192.168.1.16
     |
     | HTTPS / TCP 8220
     |
     v
elastic-siem
192.168.1.11
Fleet Server
```

## Network Security Baseline

The following observations should be compared against this baseline during future investigations:

| Observation                       | Investigation Focus                           |
| --------------------------------- | --------------------------------------------- |
| New listening port                | Identify the service and process              |
| Unexpected listening address      | Determine why the service is exposed          |
| New process with network activity | Identify process, user and parent process     |
| Unexpected remote IP              | Determine destination and purpose             |
| Unexpected outbound connection    | Correlate with process and user               |
| New DNS configuration             | Check for configuration modification          |
| Unexpected route                  | Check for unauthorized network changes        |
| New network interface             | Determine whether it was legitimately created |
| Unexpected Fleet destination      | Verify Elastic/Fleet configuration            |

## Network Investigation Correlation

Network events should be correlated with other Linux telemetry:

```text
Network Connection
        |
        +---- Source IP
        |
        +---- Destination IP
        |
        +---- Destination Port
        |
        +---- Process
        |
        +---- User
        |
        +---- Parent Process
        |
        +---- Command Line
        |
        +---- Authentication Event
        |
        +---- Auditd Event
        |
        +---- Elastic SIEM Event
```

## Baseline Security Considerations

The CatchMe Linux SOC lab requires stable endpoint addressing.

Expected lab addresses:

```text
Elastic SIEM
Hostname: elastic-siem
IP:       192.168.1.11

Linux Endpoint
Hostname: soc-linux
IP:       192.168.1.16
```

