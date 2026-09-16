# Endpoint Monitoring

## Overview

The SOC environment contains two monitored endpoints representing Windows and Linux systems.

|Endpoint|Operating System|Monitoring|
|---|---|---|
|`WIN-victim`|Windows Server 2022|Elastic Agent, Sysmon, Elastic Defend, Windows Event Logs|
|`BlueZapus-LINUX-Victim`|Ubuntu 24.04 LTS|Elastic Agent, Elastic Defend, authentication, audit, system & SSH logs|

Both endpoints are managed through Elastic Fleet and forward security telemetry to the Elastic Stack.

---

# Windows Endpoint

## WIN-victim

![WIN SERVER](images/WIN-Victim.png)

```text
OS      : Windows Server 2022
vCPU    : 1
RAM     : 2 GB
Storage : 55 GB SSD
```

`WIN-victim` acts as the primary Windows monitoring and attack-simulation target.

The endpoint was enrolled into the Windows-specific Fleet policy:

```text
BZ-WIN Agent policy 1
```

## Windows Monitoring Stack

```text
WIN-victim
│
├── Elastic Agent
├── Elastic Defend
│
├── Windows Event Logs
├── Windows Security Logs
│
└── Sysmon64
    └── sysmonconfig.xml
```

### Sysmon

Sysmon was deployed to provide additional visibility into Windows system activity.

The configuration used was based on the **Olaf Hartong Sysmon configuration**:

```text
Sysmon64
   │
   └── sysmonconfig.xml
```

This extends the telemetry available for security monitoring beyond standard Windows logs.

### Windows Event & Security Logs

Windows Event Logs and Security Logs were collected to provide visibility into authentication and other security-relevant operating system activity.

Combined with Sysmon and Elastic Defend, these sources provide multiple layers of Windows endpoint telemetry.

### Elastic Defend

Elastic Defend was integrated with the Windows endpoint to provide endpoint security telemetry directly within Elastic Security.

The overall Windows telemetry path is:

```text
Windows Activity
      │
      ├── Windows Event Logs
      ├── Security Logs
      ├── Sysmon
      └── Elastic Defend
              │
              ▼
         Elastic Agent
              │
              ▼
        Elasticsearch
              │
              ▼
       Elastic Security
```

> **Evidence:** Add screenshot of Windows agent / integration configuration.

---

# Linux Endpoint

## BlueZapus-LINUX-Victim

![LINUXSERVER](images/BlueZapus-LINUX-Victim.png)

```text
OS      : Ubuntu 24.04 LTS
vCPU    : 1
RAM     : 1 GB
Storage : 32 GB NVMe
```

`BlueZapus-LINUX-Victim` acts as the Linux monitoring target.

The endpoint was enrolled into:

```text
BZ-LINUX Agent policy
```

## Linux Monitoring Stack

The Linux endpoint was configured to provide multiple security-relevant telemetry sources.

```text
BlueZapus-LINUX-Victim
│
├── Elastic Agent
├── Elastic Defend
│
├── auth.log
├── auditd
├── System Logs
└── SSH Logs
```

### Authentication & SSH Monitoring

Linux authentication and SSH activity was collected to provide visibility into remote authentication attempts.

This telemetry allows events such as failed SSH authentication to be investigated centrally from Kibana.

![LINUXSERVER](images/endpoint-ssh-event.jpg)

Example Elastic query:

```text
system.auth.ssh.event : "Failed"
```

Endpoint filtering can be applied using:

```text
agent.name : "BlueZapus-LINUX-Victim"
```

During monitoring, the environment recorded hundreds of failed SSH authentication events from multiple external source addresses.

> **Evidence:** Add screenshot of failed SSH activity in Kibana Discover.

---

### Auditd

Auditd telemetry was included to provide additional visibility into security-relevant Linux system activity.

```text
Linux Activity
      │
      ▼
    auditd
      │
      ▼
Elastic Agent
      │
      ▼
Elasticsearch
```

This complements authentication and system logs when investigating activity on the Linux endpoint.

### Elastic Defend

Elastic Defend was also deployed to the Linux endpoint, allowing endpoint security telemetry to be integrated with the rest of the Elastic monitoring environment.

---

## Centralized Endpoint Visibility

Both operating systems feed telemetry into the same Elastic environment.

```text
             WIN-victim
                 │
        ┌────────┴────────┐
        │                 │
   Windows Logs         Sysmon
        │                 │
        └────────┬────────┘
                 │
          Elastic Agent
                 │
                 │
                 ▼
            Elasticsearch
                 ▲
                 │
          Elastic Agent
                 │
        ┌────────┴────────┐
        │                 │
     auth.log           auditd
        │                 │
        └────────┬────────┘
                 │
       LINUX-Victim
```

This provides centralized visibility across different operating systems from a single SOC monitoring platform.

---

## Endpoint Management

Both endpoints are centrally managed through Fleet:

```text
BlueZapus-Fleet-Server
          │
     ┌────┴────┐
     │         │
     ▼         ▼
 Windows     Linux
 Policy      Policy
     │         │
     ▼         ▼
WIN-victim  LINUX-Victim
```

At the time of the captured Fleet screenshot:

- `BlueZapus-LINUX-Victim` — **Healthy**
    
- `BlueZapus-Fleet-Server` — **Healthy**
    
- `WIN-victim` — **Unhealthy**
    

The Windows endpoint had previously been successfully enrolled and used for telemetry collection and attack simulation. The captured state reflects the environment near the end of the cloud deployment.

---

## Endpoint Monitoring Result

The endpoint monitoring implementation provided:

- Windows and Linux visibility from a centralized SIEM
    
- Windows Sysmon telemetry
    
- Windows Security and Event Logs
    
- Linux authentication and SSH telemetry
    
- Linux auditd telemetry
    
- Elastic Defend integration
    
- Centralized Elastic Agent management
    
- Cross-platform security event investigation
    

These endpoints provide the telemetry required for the attack detection and investigation scenarios documented in **`04-attack-detection.md`**.