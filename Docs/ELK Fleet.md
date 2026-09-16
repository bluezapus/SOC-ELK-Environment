# ELK Stack & Fleet

## Overview

The Elastic Stack acts as the central SIEM and monitoring platform of the SOC environment.

The deployment is separated into two main servers:

![SOC workflow](images/FLEET-Policy.png)

|Server|Function|
|---|---|
|`BlueZapus-ELK`|Elasticsearch, Kibana & Elastic Security|
|`BlueZapus-Fleet-Server`|Fleet Server & Elastic Agent management|

This architecture separates the SIEM workload from endpoint agent management.

---

## ELK Server

### BlueZapus-ELK

```text
OS      : Ubuntu 22.04
vCPU    : 4
RAM     : 16 GB
Storage : 80 GB NVMe
```

The server hosts the main Elastic components:

```text
BlueZapus-ELK
│
├── Elasticsearch
├── Kibana
└── Elastic Security
```

### Elasticsearch

Elasticsearch provides centralized storage and search capabilities for security telemetry collected from the monitored endpoints.

The Elasticsearch service was configured to automatically start with the operating system and verified as running using `systemd`.

```bash
systemctl status elasticsearch.service
```

> **Evidence:** Add screenshot of the running Elasticsearch service.

---

### Kibana

Kibana provides the primary web interface used by the SOC analyst.

It is used for:

- Security event searching
    
- Log analysis
    
- Detection monitoring
    
- Endpoint visibility
    
- Security investigation
    
- Fleet administration
    

The Kibana service was configured and verified as running through `systemd`.

```bash
systemctl status kibana.service
```

Kibana was exposed through:

```text
TCP/5601
```

The SOC analyst accessed the interface remotely through a web browser.

> **Evidence:** Add screenshot of the running Kibana service.

---

## Fleet Server

### BlueZapus-Fleet-Server

```text
OS      : Ubuntu 22.04
vCPU    : 1
RAM     : 4 GB
Storage : 30 GB NVMe
```

A dedicated Fleet Server was deployed to provide centralized Elastic Agent management.

Fleet Server handles:

- Agent enrollment
    
- Agent policies
    
- Integration management
    
- Agent status monitoring
    
- Centralized configuration updates
    

The Fleet service communicates using:

```text
TCP/8220
```

---

## Elastic Agents

Elastic Agent **9.5.3** was deployed across the monitored endpoints.

The Fleet interface contained three enrolled agents:

|Host|Policy|Role|
|---|---|---|
|`BlueZapus-Fleet-Server`|`Fleet Server Policy`|Fleet Server|
|`WIN-victim`|`BZ-WIN Agent policy 1`|Windows monitoring|
|`BlueZapus-LINUX-Victim`|`BZ-LINUX Agent policy`|Linux monitoring|

```text
                  Elastic Fleet
                       │
             ┌─────────┼─────────┐
             │         │         │
             ▼         ▼         ▼
         Fleet      Windows     Linux
         Server      Agent      Agent
```

> **Evidence:** Add Fleet Agents screenshot.

### Agent Status

The Fleet screenshot captured two agents in a **Healthy** state and the Windows endpoint in an **Unhealthy** state.

The Windows agent had previously been enrolled and communicating with Fleet. The captured status reflects the lab state near the end of the cloud deployment rather than being presented as a fully healthy final-state screenshot.

Keeping the original status provides an accurate record of the environment at the time of documentation.

---

## Agent Policies

Separate policies were used for Windows and Linux endpoints.

```text
Fleet
│
├── Fleet Server Policy
│
├── BZ-WIN Agent policy 1
│   └── WIN-victim
│
└── BZ-LINUX Agent policy
    └── BlueZapus-LINUX-Victim
```

Using separate endpoint policies allowed telemetry and security integrations to be configured according to each operating system.

---

## Windows Telemetry

The Windows endpoint was configured with multiple telemetry sources:

```text
WIN-victim
│
├── Elastic Agent
├── Elastic Defend
├── Windows Event Logs
├── Windows Security Logs
└── Sysmon
    └── Olaf Hartong Sysmon Configuration
```

Sysmon provides detailed Windows system activity that can be correlated with other Windows and endpoint events inside Elastic Security.

Detailed Windows endpoint configuration is documented in:

**`03-endpoints.md`**

---

## Linux Telemetry

The Linux endpoint was configured to collect security-relevant system telemetry:

```text
BlueZapus-LINUX-Victim
│
├── Elastic Agent
├── Elastic Defend
├── auth.log
├── auditd
├── System Logs
└── SSH Logs
```

This provides visibility into authentication attempts, SSH activity, system events, and endpoint security activity.

Detailed Linux monitoring is documented in:

**`03-endpoints.md`**

---

## Data Flow

Fleet Server acts as the management plane for Elastic Agents, while endpoint telemetry is forwarded to the Elastic environment for storage and analysis.

![SOC workflow](images/FLEET-Flow.png)

This separation allows Fleet to centrally manage endpoint agents while Elasticsearch remains responsible for telemetry storage and analysis.

---

## Telemetry Verification

Telemetry ingestion was verified through Kibana Discover.

For example, Linux SSH authentication failures were identified using:

```text
system.auth.ssh.event : "Failed"
```

combined with the endpoint filter:

```text
agent.name : "BlueZapus-LINUX-Victim"
```

The query returned hundreds of failed SSH authentication events from multiple external source addresses.

This confirmed the complete telemetry path:

```text
Linux Activity
      │
      ▼
Elastic Agent
      │
      ▼
Elasticsearch
      │
      ▼
Kibana Discover
      │
      ▼
SOC Analysis
```

> **Evidence:** Add screenshot of failed SSH events in Kibana Discover.

The security significance and investigation of these events are covered in **`04-attack-detection.md`**.

---

## Result

The Elastic infrastructure successfully provided:

- Centralized Windows and Linux telemetry
    
- Dedicated Fleet Server
    
- Centralized Elastic Agent management
    
- Separate Windows and Linux agent policies
    
- Sysmon telemetry collection
    
- Linux authentication and SSH visibility
    
- Elastic Defend integration
    
- Centralized log searching through Kibana
    
- Security monitoring through Elastic Security
    

This infrastructure forms the monitoring foundation used by the attack detection and investigation components of the SOC lab.