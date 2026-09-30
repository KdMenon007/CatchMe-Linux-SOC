# Network Baseline

## Endpoint Identity

| Field | Value |
|---|---|
| Hostname | `soc-linux` |
| Endpoint IP Address | `192.168.1.16` |
| Endpoint Role | Linux SOC Monitoring Endpoint |
| SIEM Hostname | `elastic-siem` |
| SIEM IP Address | `192.168.1.11` |
| SIEM Role | Elastic SIEM / Kibana / Fleet Server |
| Network | `192.168.1.0/24` |

## Network Architecture

```text
                    Local Network
                    192.168.1.0/24
                           |
              +------------+------------+
              |                         |
              |                         |
              v                         v
       ELASTIC SIEM               LINUX ENDPOINT
       elastic-siem                  soc-linux
       192.168.1.11                192.168.1.16
              |                         |
              |                         |
       Elasticsearch               Elastic Agent
       Kibana                      Auditd
       Fleet Server                Linux Telemetry

## Network Configuration Baseline

The Linux endpoint was configured with:

* Hostname: `soc-linux`
* IP address: `192.168.1.16`
* Elastic SIEM: `192.168.1.11`
* Network range: `192.168.1.0/24`
* Elastic/Fleet hostname: `elastic-siem`

The hostname resolution between the Linux endpoint and Elastic SIEM is expected to resolve as:

elastic-siem → 192.168.1.11
```

## Network Components

| Component      | Hostname       | IP             | Function                 |
| -------------- | -------------- | -------------- | ------------------------ |
| Elastic SIEM   | `elastic-siem` | `192.168.1.11` | SIEM infrastructure      |
| Linux Endpoint | `soc-linux`    | `192.168.1.16` | Monitored endpoint       |
| Fleet Server   | `elastic-siem` | `192.168.1.11` | Elastic Agent management |

## Expected Elastic Communication

The Linux endpoint communicates with the Elastic infrastructure for telemetry and agent management.

```text
soc-linux
   |
   | Elastic Agent
   |
   +------ HTTPS ------> elastic-siem:8220
   |                     Fleet Server
   |
   +------ HTTPS ------> Elastic infrastructure
```

Fleet Server communication uses:

```text
https://elastic-siem:8220
```

## Network Baseline Collection

The following information was collected from the Linux endpoint:

### Network Interfaces

Collected using:

```bash
ip -br addr
```

Purpose:

* Identify active interfaces
* Identify assigned IP addresses
* Establish the normal endpoint network identity

### Routing

Collected using:

```bash
ip route
```

Purpose:

* Identify the default route
* Identify connected networks
* Establish the normal routing state

### DNS

Collected using:

```bash
resolvectl status
```

Purpose:

* Identify configured DNS servers
* Establish the normal DNS configuration
* Support investigation of suspicious DNS activity

### Listening Ports

Collected using:

```bash
sudo ss -tulpen
```

Purpose:

* Identify TCP listeners
* Identify UDP listeners
* Identify processes associated with listening ports
* Establish the normal network attack surface

### Active Connections

Collected using:

```bash
sudo ss -tunap
```

Purpose:

* Identify active connections
* Identify remote endpoints
* Correlate network connections with local processes
* Establish normal network activity

## Security-Relevant Network Baseline

During future investigations, the following changes should be treated as investigation points:

| Observation                           | Investigation Question                                   |
| ------------------------------------- | -------------------------------------------------------- |
| New listening port                    | What service opened the port?                            |
| New process listening on a known port | Is the process legitimate?                               |
| Unexpected outbound connection        | Why is the endpoint communicating with this destination? |
| Unexpected remote IP                  | Is the destination expected?                             |
| New DNS server                        | Was the DNS configuration modified?                      |
| New network interface                 | Why was the interface created or enabled?                |
| Unexpected routing change             | Was routing modified by an attacker or administrator?    |
| Unknown process with network activity | What executed the process and under which user?          |

## Network Investigation Correlation

Network activity should be correlated with other Linux telemetry.

```text
Network Connection
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

## Baseline Verification

The following conditions define the expected network identity of the lab:

```text
Linux Endpoint
Hostname: soc-linux
IP:       192.168.1.16

Elastic SIEM
Hostname: elastic-siem
IP:       192.168.1.11

Fleet Server
Host:     https://elastic-siem:8220
```

