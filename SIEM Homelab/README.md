# SIEM Homelab - Phase 1 & 2
*A hands-on security engineering and SOC analysis environment built to simulate real enterprise telemetry, detection workflows, and adversary activity.*

## Project Overview
This project documents the design,m build, and evolution of a full SIEM homelab environment incorporating Splunk, windows active directory, linux systems, and a vulnerable web application. The goal is develop practical skills in:
- SIEM engineering
- SOC analyst workflows
- Detection Engineering
- Threat Hunting
- Log Pipeline Architecture
- Dashboarding and reporting
This lab supports my career pathway towards Cybersecurity Professional.

## Purpose
Build a realistic environment that produces meaningful security telemetry and allows for:
- Designing and maintaining a SIEM pipeline
- Onboarding and normalizing logs from diverse systems
- Developing SPL queries, correlation searches, and detections
- Executing attack simulation using Kali Linux
- Creating dashboards for authentication, Endpoint activity, network behavior, and threat patterns
- Practicing incident analysis and documentation

## Provisions (Phase 1)

### Virtual Machines:

| System | Role | Logging |
|--------|------|---------|
| Ubuntu Server | Splunk Indexer/Search Head | Native |
| Windows Server | AD Environment | Splunk UF |
| Windows Workstation | Domain-joined endpoint | Splunk UF |
| Metasploitable 2 | Vulnerable web app | Splunk UF |
| Kali Linux | Attack box | Manual Log Generation |

### Network Topology
```
Flat Network
 ├── Ubuntu Splunk Server
 ├── Windows Server (DC)
 ├── Windows Workstation
 ├── Metasploitable 2
 └── Kali Linux
 ```

### Data Pipeline Architecture
```
[Endpoints & Servers]
     ↓ (UF / Native Logs)
[Splunk Indexer]
     ↓
[Splunk Search Head]
     ↓
[Dashboards, Alerts, Detections]
```


## Construction Notice
🚧 This project is in the early planning stage. Systems are not yet deployed, and documentation will expand as the homelab is built and telemetry begins flowing.
