# Architecture

## Overview

**SOC ELK Environment** is a cloud-based SOC lab designed to simulate centralized security monitoring, endpoint telemetry collection, attack simulation, detection, investigation, and incident management.

The SOC infrastructure was deployed on **Vultr**, while a local **Arch Linux** workstation was used as the attacker system.

The environment combines:

- Elastic Stack
    
- Elastic Fleet
    
- Windows & Linux endpoints
    
- Sysmon
    
- Elastic Defend
    
- Mythic C2
    
- osTicket
    
- Arch Linux attack workstation
    

---

## Network Topology

![SOC ELK Environment Topology](images/topology.png)

The core infrastructure was deployed inside a Vultr environment using a private VPC network.

|Network|Configuration|
|---|---|
|Private Network|`172.31.0.0/24`|
|Host Range|`172.31.0.1 - 172.31.0.254`|
|Subnet Mask|`255.255.255.0`|
|Cloud Provider|Vultr|
|Region|New Jersey, US|

Public IP addresses are intentionally omitted from this repository.

---

## Infrastructure

![ELK SERVER](images/BlueZapus-ELK-SERVER.png)

![ELK SERVER](images/BlueZapus-Fleet-Server.png)

![LINUXSERVER](images/BlueZapus-LINUX-Victim.png)

![WIN SERVER](images/WIN-Victim.png)

![ELK SERVER](images/BlueZapus-Mythic.png)

![ELK SERVER](images/BlueZapus-OsTickets.png)

| System                   | OS                  |  vCPU |   RAM |    Storage | Role                                     |
| ------------------------ | ------------------- | ----: | ----: | ---------: | ---------------------------------------- |
| `BlueZapus-ELK`          | Ubuntu 22.04        |     4 | 16 GB | 80 GB NVMe | Elasticsearch, Kibana & Elastic Security |
| `BlueZapus-Fleet-Server` | Ubuntu 22.04        |     1 |  4 GB | 30 GB NVMe | Elastic Fleet Server                     |
| `WIN-victim`             | Windows Server 2022 |     1 |  2 GB |  55 GB SSD | Windows monitored endpoint               |
| `BlueZapus-LINUX-Victim` | Ubuntu 24.04 LTS    |     1 |  1 GB | 32 GB NVMe | Linux monitored endpoint                 |
| `BlueZapus-MYTHIC`       | Ubuntu 22.04        |     2 |  4 GB |  80 GB SSD | Mythic C2 infrastructure                 |
| `BlueZapus-osTicket`     | Windows Server 2022 |     2 |  4 GB |  80 GB SSD | Incident ticketing                       |
| `Attacker Workstation`   | Arch Linux          | Local | Local |      Local | Attack simulation                        |
![elastic](images/elastic-service.jpeg)
![elastic](images/kibana-service.jpeg)
---
## Component Architecture

### SIEM

`BlueZapus-ELK` is the central monitoring server.

```text
Elasticsearch
     │
     ▼
   Kibana
     │
     ▼
Elastic Security
```

It provides centralized storage, log searching, security monitoring, detection, and investigation.

---

### Fleet Management

![SOC Fleet](images/fleet-management.png)

---

### Monitored Endpoints

Two endpoints provide security telemetry to the Elastic environment.

```text
WIN-victim
├── Elastic Agent
├── Sysmon
├── Windows Event Logs
├── Security Logs
└── Elastic Defend


BlueZapus-LINUX-Victim
├── Elastic Agent
├── auth.log
├── auditd
├── System Logs
├── SSH Logs
└── Elastic Defend
```

Windows Sysmon telemetry was configured using the **Olaf Hartong Sysmon configuration**.

---

## Attack Infrastructure

Attack simulations were performed from a local **Arch Linux** workstation.

```text
Arch Linux
     │
     ├── Crowbar
     │     │
     │     └── RDP Attack Simulation
     │
     ▼
 WIN-victim
```

A separate Mythic server was also deployed to provide Command & Control infrastructure.

```text
BlueZapus-MYTHIC
       │
       │ Mythic C2 / Apollo
       ▼
   WIN-victim
```

This allowed endpoint activity generated during controlled attack simulations to be observed from the SOC monitoring environment.

---

## Security Monitoring Flow

Endpoint telemetry is collected using Elastic Agent and forwarded to the Elastic environment.

![SOC Fleet](images/security-monitoring.png)

Fleet Server provides the management plane for the endpoint agents.

The SOC analyst accesses Kibana through the web interface to search events, monitor security activity, and investigate detections.

---

## Firewall Configuration

A Vultr Firewall Group was attached to the lab infrastructure.

![SOC Firewall](images/SOC-Firewall.png)

|Port|Protocol|Purpose|
|--:|---|---|
|`22`|SSH|Linux administration|
|`80`|HTTP|Web services|
|`443`|HTTPS|Secure web services|
|`3389`|RDP|Windows administration|
|`5044`|TCP|Log ingestion|
|`5601`|TCP|Kibana Web UI|
|`7443`|TCP|Mythic Web UI|
|`8220`|TCP|Elastic Fleet Server|
|`9200`|TCP|Elasticsearch|

Traffic not matching the allowed inbound rules was dropped by the default firewall rule.

> **Security Note:** During development, several service ports were exposed broadly to simplify lab connectivity. In a production environment, administrative and backend services such as Elasticsearch, Fleet Server, SSH, RDP, and Kibana should be restricted to trusted networks, VPN access, or specific source addresses.

---

## Incident Management

`BlueZapus-osTicket` was deployed as the incident management platform.

The intended workflow was:

```text
Elastic Security
       │
       ▼
Detection / Alert
       │
       ▼
API Integration
       │
       ▼
    osTicket
       │
       ▼
Incident Ticket
```

The osTicket deployment was completed, but automated API integration between Elastic and osTicket was not completed before the cloud environment was discontinued.

---

## Overall SOC Workflow

![SOC workflow](images/overall-workflow.png)

The architecture demonstrates the complete security monitoring lifecycle from **attack simulation and telemetry collection to centralized detection, investigation, and incident management**.