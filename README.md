# Microsoft Sentinel SOC Lab

Hands-on cybersecurity lab built around Microsoft Sentinel, Log Analytics, Windows Server, Ubuntu, and automation playbooks.  
The goal is to detect brute-force RDP and SSH attempts, generate incidents, send email notifications, and automate response actions.

## Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Lab Modules](#lab-modules)
- [Windows RDP Detection](#windows-rdp-detection)
- [Ubuntu SSH Detection](#ubuntu-ssh-detection)
- [Automation and Playbooks](#automation-and-playbooks)
- [Email Alerts in Outlook](#email-alerts-in-outlook)
- [Results](#results)
- [Future Work](#future-work)
- [Author](#author)

## Overview

This lab demonstrates an end-to-end SIEM workflow:
- build a Microsoft Sentinel environment,
- onboard Windows and Linux virtual machines,
- validate log ingestion,
- create analytics rules for failed RDP and SSH attempts,
- generate incidents,
- send alert emails to Outlook,
- and trigger a playbook to block source IPs after repeated failed attempts.

## Architecture

The lab includes:
- VMware Workstation as the virtualization platform.
- A Windows Server VM as the RDP target.
- An Ubuntu VM as the SSH target.
- A Log Analytics Workspace connected to Microsoft Sentinel.
- MMA / legacy agent onboarding for endpoint log collection.
- `SecurityEvent` for Windows logs.
- `Syslog` for Ubuntu logs.
- Analytics rules for detection.
- Incidents for response.
- Playbooks for automation and IP blocking.
<img width="580" height="857" alt="image" src="https://github.com/user-attachments/assets/82cd4243-5ad1-4e12-ab52-ee344308b1ab" />


## Lab Modules

### Module 1 — Sentinel Environment Setup
- Create the resource group.
<img width="705" height="522" alt="image" src="https://github.com/user-attachments/assets/a0c31d44-2a7b-4e55-aa12-336cd4ec3c1f" />

- Create the Log Analytics Workspace.
<img width="917" height="771" alt="image" src="https://github.com/user-attachments/assets/1a02a2f3-02cd-416d-993b-e42a05335a32" />

- Add Microsoft Sentinel to the workspace.
<img width="1852" height="968" alt="image" src="https://github.com/user-attachments/assets/408d80ee-6419-43bc-a830-6d9906d20fed" />


### Module 2 — Preparing VM Machines
- Install the Windows Server VM.
<img width="360" height="261" alt="image" src="https://github.com/user-attachments/assets/5546e102-6a65-4144-b096-21219e74eeb5" />
<img width="753" height="197" alt="image" src="https://github.com/user-attachments/assets/ddcf27bc-0e6e-4950-aa27-14bc5ea0e506" />

- Enable Remote Desktop.
<img width="797" height="493" alt="image" src="https://github.com/user-attachments/assets/e35412ca-8d49-4fae-8bab-8eaf3a4321bb" />

- Install the Ubuntu VM.
<img width="932" height="221" alt="image" src="https://github.com/user-attachments/assets/88351b2b-a256-4377-94e4-ea1938247735" />

- Prepare a test event on each machine.
<img width="953" height="72" alt="image" src="https://github.com/user-attachments/assets/3ddd9236-eb4b-41d2-9633-e08377c27423" />
<img width="828" height="647" alt="image" src="https://github.com/user-attachments/assets/e51f5fcb-e71f-4c0d-8ac0-f584d4375f0d" />


### Module 3 — Onboarding and Log Validation
- Install MMA / legacy agent on Windows.
<img width="1047" height="187" alt="image" src="https://github.com/user-attachments/assets/7f56ae0e-49a9-4ca3-9695-5ff3203f00a7" />

- Install MMA / legacy agent on Ubuntu.
<img width="1535" height="732" alt="image" src="https://github.com/user-attachments/assets/0d56d584-a054-4b09-9781-539044284922" />

- Validate that logs are ingested in Sentinel.
<img width="1172" height="763" alt="image" src="https://github.com/user-attachments/assets/1b18c790-da74-45f9-9c18-376712de7093" />


### Module 4 — Threat Detection with Analytics Rules
- Create analytics rules for failed RDP attempts.
<img width="1646" height="857" alt="image" src="https://github.com/user-attachments/assets/d9158707-00ee-43cc-9013-0f179c900797" />

- Configure Windows auditing.
- Create analytics rules for failed SSH attempts.
<img width="1595" height="881" alt="image" src="https://github.com/user-attachments/assets/7c1aee01-4c1e-4ebc-8f72-f5fdac704368" />

- Install and activate the required connector.

### Module 5 — Automation and Playbook
- Create a playbook for IP blocking after 10 failed attempts.
- Connect the playbook to an analytics rule.
- Trigger and test the playbook.
- Automate alerting and response.

## Windows RDP Detection

Windows security events are collected in the `SecurityEvent` table.
<img width="1518" height="591" alt="image" src="https://github.com/user-attachments/assets/11e4240b-4d4f-4ebc-a109-fda89d11d54d" />


## Ubuntu SSH Detection

Ubuntu logs are collected in the `Syslog` table.
<img width="693" height="617" alt="image" src="https://github.com/user-attachments/assets/151d9b09-f443-43b6-b489-a5700d3e8ee9" />
<img width="1612" height="837" alt="image" src="https://github.com/user-attachments/assets/42c459c2-61ac-491b-aab5-4fb8a75ae986" />



## Automation and Playbooks

The playbook is used to:
- receive the incident,
- extract the source IP,
- check if the number of failed attempts is greater than 10,
- and block the IP if the threshold is exceeded.

The playbook is created from the Sentinel automation blade and linked to the incident workflow.

## Email Alerts in Outlook

Each generated incident sends an email notification to Outlook.

### Workflow
1. A failed RDP or SSH attempt is detected.
2. Microsoft Sentinel creates an incident.

<img width="1821" height="925" alt="image" src="https://github.com/user-attachments/assets/96575f4c-2a27-4a68-833f-65012c9e6f91" />
<img width="1818" height="852" alt="image" src="https://github.com/user-attachments/assets/eaced577-d50c-4655-8006-a7b3e859b6e9" />



This makes it easy to monitor alerts without opening Sentinel constantly.


## Results

This lab demonstrates:
- Windows RDP brute-force detection.
- Ubuntu SSH brute-force detection.
- Log ingestion into Microsoft Sentinel.
- Incident creation from analytics rules.
- Email notifications sent to Outlook.
- Automatic response via playbooks.

## Future Work

Possible next steps:
- Improve SSH log parsing.
- Add IP reputation enrichment.
- Automate firewall integration more deeply.
- Add workbooks for visualization.
- Add more detection rules for lateral movement or privilege escalation.

## Author

Created by: **ABIDI OTHMANE**  
Cybersecurity Engineering Student  
Focused on SIEM, SOC operations, cloud security, and network security.

## License

This project is shared for educational and lab purposes.
