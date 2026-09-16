# Attack Simulation & Detection

## Overview

The SOC lab includes controlled attack simulations to generate security telemetry and validate monitoring capabilities within the Elastic environment.

Two attack components were used:

|System|Tool|Purpose|
|---|---|---|
|Arch Linux Workstation|Crowbar|RDP authentication attack simulation|
|`BlueZapus-MYTHIC`|Mythic C2 / Apollo|Command-and-Control simulation against Windows|

The generated endpoint activity was monitored through Elastic Security and Kibana.

---

# Attack Workstation

The primary attacker workstation was a local machine running **Arch Linux**.

```text
Attacker Workstation
│
├── Arch Linux
│
└── Crowbar
```

Crowbar was used to generate controlled RDP authentication activity against the Windows endpoint.

```text
Arch Linux
    │
    │ Crowbar
    │ RDP Authentication Activity
    ▼
WIN-victim
```

The purpose of the simulation was to generate authentication-related activity that could be observed from the SOC monitoring environment.

---

# Mythic C2 Simulation

A dedicated server was deployed for Mythic:

```text
BlueZapus-MYTHIC
│
├── Ubuntu 22.04
├── Mythic v3.4.36
└── Apollo
```

Mythic was used to simulate Command-and-Control activity against the Windows endpoint.

The lab successfully established callbacks from:

```text
Host     : WIN-VICTIM
User     : Administrator
Domain   : WIN-VICTIM
C2       : Mythic
Agent    : Apollo
```

This demonstrated a working C2 communication channel between the monitored Windows endpoint and the Mythic infrastructure.

> **Evidence:** Add screenshot of Mythic Active Callbacks showing `WIN-VICTIM`.

---

## C2 Workflow

![C2 workflow](images/C2-workflow.png)

![SOC workflow](images/C2.jpeg)
The Windows endpoint remained part of the Elastic monitoring environment while C2 activity was generated.

This allowed offensive activity and defensive telemetry collection to operate within the same controlled lab.

---

# Linux SSH Monitoring

The Linux endpoint also generated security-relevant authentication telemetry.

Failed SSH authentication events were investigated through **Kibana Discover**.

The monitored endpoint was filtered using:

```text
agent.name : "BlueZapus-LINUX-Victim"
```

Failed SSH authentication activity was identified using:

```text
system.auth.ssh.event : "Failed"
```

The resulting dataset contained hundreds of failed authentication events.

Observed fields included:

```text
@timestamp
system.auth.ssh.event
related.user
source.ip
source.geo.country_name
```

The events included repeated failed login attempts against accounts such as:

```text
root
user
```

from multiple external source addresses.

> **Evidence:** Add screenshot of failed SSH authentication events in Kibana Discover.

![SOC event](images/endpoint-ssh-event.jpg)
---

# Investigation Workflow

The investigation process followed a basic SOC workflow:

```text
Security Activity
       │
       ▼
Endpoint Telemetry
       │
       ▼
Elasticsearch
       │
       ▼
Kibana Discover
       │
       ▼
Filter / Query Events
       │
       ▼
Analyze
 ├── Timestamp
 ├── Source IP
 ├── Username
 ├── Event Type
 └── Source Location
       │
       ▼
SOC Investigation
```

Rather than reviewing isolated logs directly on each endpoint, telemetry could be searched and correlated from the centralized Elastic environment.

---

## Example: SSH Authentication Investigation

The Linux SSH activity demonstrated how centralized telemetry can be used to investigate authentication anomalies.

### Step 1 — Identify the Endpoint

```text
agent.name : "BlueZapus-LINUX-Victim"
```

### Step 2 — Filter Failed SSH Activity

```text
system.auth.ssh.event : "Failed"
```

### Step 3 — Review Relevant Fields

The investigation focused on:

```text
Timestamp
Source IP
Source Country
Target User
Authentication Result
```

### Step 4 — Identify Repeated Activity

Repeated failed authentication attempts from external sources were visible in the collected telemetry.

This demonstrates the ability to move from raw endpoint logs to centralized security investigation using Elastic.

---

# Attack & Monitoring Coverage

The lab demonstrated multiple security monitoring scenarios:

|Scenario|Source|Target|Visibility|
|---|---|---|---|
|RDP authentication simulation|Arch Linux / Crowbar|`WIN-victim`|Windows endpoint telemetry|
|C2 simulation|Mythic / Apollo|`WIN-victim`|Windows endpoint activity|
|Failed SSH authentication|External sources|`BlueZapus-LINUX-Victim`|Elastic / Kibana Discover|

---

## Detection vs. Observed Telemetry

Not every observed security event in the lab is presented as an automated Elastic detection.

The project distinguishes between:

**Observed Telemetry**

Events collected and manually investigated through Kibana.

and

**Detection Alerts**

Events identified by configured Elastic Security detection rules.

This distinction is intentionally maintained to avoid presenting manually investigated telemetry as an automated detection.

---

# SOC Analysis Flow

```text
              ATTACK ACTIVITY
                     │
       ┌─────────────┼──────────────┐
       │             │              │
    Crowbar       Mythic C2      SSH Activity
       │             │              │
       ▼             ▼              ▼
 WIN-victim     WIN-victim     LINUX-Victim
       │             │              │
       └─────────────┼──────────────┘
                     ▼
                Elastic Agent
                     │
                     ▼
                Elasticsearch
                     │
                     ▼
                  Kibana
                     │
                     ▼
             Security Analysis
                     │
              ┌──────┴──────┐
              ▼             ▼
           Detection    Investigation
```

---

# Result

The attack simulation and monitoring environment demonstrated:

- Controlled attack generation from an Arch Linux workstation
    
- RDP authentication activity using Crowbar
    
- Deployment of dedicated Mythic C2 infrastructure
    
- Successful Apollo callbacks from `WIN-VICTIM`
    
- Centralized Windows and Linux telemetry collection
    
- Identification of repeated failed SSH authentication attempts
    
- Source IP and geographic context analysis
    
- Security event filtering using Elastic
    
- Basic SOC investigation workflow
    

These scenarios demonstrate the relationship between **offensive activity, endpoint telemetry, centralized monitoring, and SOC investigation** within the lab environment.