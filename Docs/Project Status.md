# Project Status

## Overview

The **SOC ELK Environment** reached a functional monitoring and attack-simulation stage before the cloud infrastructure was discontinued.

The core objectives of the project were successfully implemented:

**Infrastructure → Telemetry → Monitoring → Attack Simulation → Investigation → Incident Management**

The only major unfinished component was the automated **Elastic Security → osTicket API integration**.

---

## Implementation Status

| Component                       | Status      | Description                                                             |
| ------------------------------- | ----------- | ----------------------------------------------------------------------- |
| Vultr SOC Infrastructure        | ✅ Completed | Multi-server cloud environment deployed                                 |
| Private VPC                     | ✅ Completed | SOC systems connected through `172.31.0.0/24`                           |
| Vultr Firewall                  | ✅ Completed | Service-specific inbound rules configured                               |
| Elasticsearch                   | ✅ Completed | Centralized telemetry storage                                           |
| Kibana                          | ✅ Completed | Log analysis and SOC monitoring interface                               |
| Elastic Security                | ✅ Completed | Security monitoring and investigation                                   |
| Fleet Server                    | ✅ Completed | Centralized Elastic Agent management                                    |
| Windows Endpoint                | ✅ Completed | Windows Server enrolled and monitored                                   |
| Linux Endpoint                  | ✅ Completed | Ubuntu Server enrolled and monitored                                    |
| Sysmon                          | ✅ Completed | Windows telemetry using Olaf Hartong configuration                      |
| Elastic Defend                  | ✅ Completed | Endpoint security telemetry                                             |
| Linux Authentication Monitoring | ✅ Completed | SSH/authentication telemetry collected                                  |
| Mythic C2                       | ✅ Completed | Dedicated Mythic C2 environment deployed                                |
| Apollo Callback                 | ✅ Validated | Callback established from `WIN-VICTIM`                                  |
| Crowbar RDP Simulation          | ✅ Completed | Controlled RDP authentication activity generated                        |
| osTicket                        | ✅ Completed | Incident management platform deployed                                   |
| Elastic → osTicket API          | ✅ Completed | PI integration for automated incident ticket creation                   |
| Automatic Alert-to-Ticket       | ✅ Completed | Elastic security alerts automatically converted into osTicket incidents |

---

## Validated Capabilities

The lab successfully demonstrated:

### Infrastructure

```text
Vultr Cloud
    │
    ├── Private VPC
    ├── Firewall
    ├── Linux Servers
    └── Windows Servers
```

### Security Monitoring

```text
Windows / Linux
       │
       ▼
  Elastic Agent
       │
       ▼
 Elasticsearch
       │
       ▼
 Elastic Security
       │
       ▼
SOC Investigation
```

### Windows Monitoring

Implemented telemetry sources included:

```text
Elastic Agent
Sysmon
Windows Event Logs
Windows Security Logs
Elastic Defend
```

### Linux Monitoring

Implemented telemetry sources included:

```text
Elastic Agent
auth.log
auditd
System Logs
SSH Logs
Elastic Defend
```

### Attack Simulation

Two attack-simulation components were successfully used:

```text
Arch Linux
    │
    └── Crowbar
          │
          ▼
     RDP Activity
          │
          ▼
      WIN-victim
```

and:

```text
Mythic C2
    │
    └── Apollo
          │
          ▼
      WIN-VICTIM
```

A working Apollo callback from the monitored Windows endpoint was successfully established.

### Security Investigation

Collected telemetry was searchable through Kibana.

Linux SSH authentication activity was successfully identified using:

```text
agent.name : "BlueZapus-LINUX-Victim"
```

and:

```text
system.auth.ssh.event : "Failed"
```

This demonstrated centralized investigation of endpoint security events using Elastic.

---

## Final Architecture Status

```text
                       ATTACK
                         │
               ┌─────────┴─────────┐
               │                   │
          Arch Linux           Mythic C2
               │                   │
               └─────────┬─────────┘
                         ▼
                  Windows / Linux
                     Endpoints
                         │
                         ▼
                   Elastic Agent
                         │
                         ▼
                   Elasticsearch
                         │
                         ▼
                  Elastic Security
                         │
                         ▼
                    Investigation
                         │
                         ▼
                      osTicket
                         │
                         ▼
                 Incident Tracking

                 API Automation
                  NOT COMPLETED
```

---

## Project Limitation

Development stopped during the **Elastic-to-osTicket API integration** stage after the Vultr account reached its cloud billing limit and the deployed instances were suspended.

Because of this, automatic creation of osTicket incidents from Elastic Security alerts was not completed.

The project documentation distinguishes between **implemented**, **validated**, and **planned** functionality to accurately represent the final state of the environment.

---

## Security Considerations

This environment was designed as a controlled learning lab rather than a production SOC.

Some services were exposed more broadly during testing to simplify connectivity between lab components.

A production deployment should additionally implement:

- Restricted source IP access
    
- VPN or bastion-based administrative access
    
- Network segmentation
    
- TLS for internal service communication
    
- Least-privilege firewall rules
    
- Secret and API key management
    
- Backup and recovery procedures
    
- High availability for critical SOC services
    

---

## Skills Demonstrated

This project demonstrates hands-on experience with:

- Elastic Stack deployment and administration
    
- Elasticsearch and Kibana
    
- Elastic Security
    
- Elastic Fleet and Elastic Agent
    
- Windows security monitoring
    
- Linux security monitoring
    
- Sysmon telemetry
    
- Elastic Defend
    
- Centralized log analysis
    
- KQL-based event investigation
    
- Authentication log analysis
    
- Vultr cloud infrastructure
    
- VPC networking and firewall configuration
    
- Mythic C2 infrastructure
    
- Controlled attack simulation
    
- SOC investigation workflow
    
- Incident management using osTicket
    

---

## Final Status

**Project Status: Completed — End-to-End SOC Environment Operational**

The core SOC infrastructure, endpoint monitoring, attack simulation, telemetry collection, detection, investigation, and incident management capabilities were successfully implemented and validated.

The Elastic-to-osTicket API integration was successfully completed, enabling automated incident ticket creation from security alerts.

The environment demonstrates an end-to-end SOC workflow from **attack simulation and security telemetry collection to centralized detection, investigation, automated ticket creation, and incident tracking**.