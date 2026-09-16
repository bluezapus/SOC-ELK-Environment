# Incident Response & Ticketing

## Overview

The final component of the SOC environment was an incident management system based on **osTicket**.

`BlueZapus-osTicket` was deployed to provide a centralized platform for documenting, assigning, tracking, and managing security incidents identified during SOC monitoring.

```text
Elastic Security
       │
       ▼
Security Event / Alert
       │
       ▼
SOC Investigation
       │
       ▼
    osTicket
       │
       ▼
Incident Tracking
```

---

## osTicket Server

### BlueZapus-osTicket

![OS Tickets](images/BlueZapus-OsTickets.png)

```text
OS      : Windows Server 2022
vCPU    : 2
RAM     : 4 GB
Storage : 80 GB SSD
Role    : Incident Management
```

![OS Tickets](images/OS-Tickets-Installer.jpg)

osTicket was successfully installed and configured as the ticketing platform for the SOC environment.

> **Evidence:** Add screenshot of the osTicket interface.

---

## Purpose

The ticketing system was introduced to extend the lab beyond detection and investigation.

Instead of stopping at an Elastic alert, security events could be documented as incidents and tracked through a basic incident response workflow.

```text
Detection
    │
    ▼
Investigation
    │
    ▼
Incident Creation
    │
    ▼
Assignment
    │
    ▼
Analysis / Response
    │
    ▼
Resolution
```

This provides a basic separation between **security monitoring** and **incident management**.

---

## Incident Workflow

The intended SOC workflow was:

```text
Elastic Security
       │
       ▼
Detection / Alert
       │
       ▼
SOC Analyst Review
       │
       ▼
Incident Ticket
       │
       ├── Event Details
       ├── Source / Target
       ├── Severity
       ├── Investigation Notes
       └── Response Status
       │
       ▼
Incident Tracking
       │
       ▼
Resolution
```

osTicket provides the case-management layer where relevant investigation information can be maintained outside the SIEM.

---

## Planned Elastic Integration

The next development stage was an API-based integration between **Elastic Security** and **osTicket**.

The planned architecture was:

```text
Elastic Security
       │
       │ Detection Alert
       ▼
Integration / API
       │
       ▼
  osTicket API
       │
       ▼
Automatic Ticket Creation
```

The goal was to automatically convert selected Elastic Security alerts into osTicket incidents.

A generated ticket could then contain information such as:

```text
Alert Name
Timestamp
Severity
Source IP
Destination / Endpoint
Detection Details
Investigation Reference
```

---

## Integration Status

The osTicket server and ticketing platform were successfully deployed.

However, development of the Elastic-to-osTicket API automation was **not completed** before the Vultr environment became unavailable due to the cloud billing limit.

Therefore:

|Component|Status|
|---|---|
|osTicket Server|✅ Completed|
|osTicket Installation|✅ Completed|
|Web Interface|✅ Completed|
|Incident Management Platform|✅ Completed|
|Elastic → osTicket Design|✅ Planned|
|API Integration|⚠️ Not Completed|
|Automatic Alert-to-Ticket Creation|⚠️ Not Completed|

The project intentionally documents this limitation rather than presenting the planned automation as a completed feature.

---

## Manual Incident Handling

Even without automated API integration, the intended manual workflow remains:

```text
Elastic Alert / Suspicious Event
            │
            ▼
     Analyst Investigation
            │
            ▼
      Validate Incident
            │
            ▼
    Create osTicket Case
            │
            ▼
   Document Investigation
            │
            ▼
      Track Resolution
```

This represents a basic SOC Tier 1 escalation and incident documentation process.

---

## Future Improvement

If the environment were continued, the next implementation stage would be:

**Elastic Alert → API Processing → osTicket Ticket Creation**

Additional improvements could include:

- Automatic ticket creation based on selected detections
    
- Alert severity mapping to ticket priority
    
- Endpoint and source IP information included automatically
    
- Detection metadata attached to the ticket
    
- Ticket assignment based on incident category
    
- Ticket status synchronization
    
- Investigation notes and evidence tracking
    

---

## Result

The incident management component demonstrates the design of a SOC workflow that extends beyond SIEM monitoring:

**Detection → Investigation → Incident Documentation → Tracking → Resolution**

Although automated Elastic-to-osTicket integration was not completed, the deployed osTicket environment provides the foundation for integrating SIEM detections with a structured incident management workflow.