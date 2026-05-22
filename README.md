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
- [Screenshots](#screenshots)
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

## Lab Modules

### Module 1 — Sentinel Environment Setup
- Create the resource group.
- Create the Log Analytics Workspace.
- Add Microsoft Sentinel to the workspace.

### Module 2 — Preparing VM Machines
- Install the Windows Server VM.
- Enable Remote Desktop.
- Install the Ubuntu VM.
- Prepare a test event on each machine.

### Module 3 — Onboarding and Log Validation
- Install MMA / legacy agent on Windows.
- Install MMA / legacy agent on Ubuntu.
- Validate that logs are ingested in Sentinel.

### Module 4 — Threat Detection with Analytics Rules
- Create analytics rules for failed RDP attempts.
- Configure Windows auditing.
- Create analytics rules for failed SSH attempts.
- Install and activate the required connector.

### Module 5 — Automation and Playbook
- Create a playbook for IP blocking after 10 failed attempts.
- Connect the playbook to an analytics rule.
- Trigger and test the playbook.
- Automate alerting and response.

## Windows RDP Detection

Windows security events are collected in the `SecurityEvent` table.

### Example KQL
```kusto
SecurityEvent
| where EventID == 4625
| extend TargetUserName = tostring(TargetUserName)
| extend IpAddress = tostring(IpAddress)
| where isnotempty(TargetUserName)
| summarize FailedAttempts = count() by TargetUserName, Computer, IpAddress
| where FailedAttempts > 3
```

This rule detects repeated failed RDP logons and creates a Sentinel incident.

## Ubuntu SSH Detection

Ubuntu logs are collected in the `Syslog` table.

### Example KQL
```kusto
Syslog
| where Facility in ("auth", "authpriv")
| where SyslogMessage has "fail" or SyslogMessage has "invalid" or SyslogMessage has "password"
| extend SourceIP = extract(@"from\s+([0-9]{1,3}(?:\.[0-9]{1,3}){3})", 1, SyslogMessage)
| extend TargetUser = extract(@"for\s+(\S+)\s+from", 1, SyslogMessage)
| summarize AttemptCount = count() by Computer, SourceIP, TargetUser
| where AttemptCount > 3
```

This rule detects repeated SSH login failures on Ubuntu.

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
3. A playbook or automation rule sends an email.
4. The alert arrives in Outlook.

### Email content
- Incident title.
- Severity.
- Time of detection.
- Affected host.
- Source IP.
- Rule name.
- Link to the incident.

This makes it easy to monitor alerts without opening Sentinel constantly.

## Screenshots

Place the screenshots in an `images/` folder and reference them here in this order:

### 1. Sentinel environment setup
Insert the screenshot showing:
- Resource group creation.
- Log Analytics Workspace creation.
- Microsoft Sentinel added to the workspace.

### 2. Virtual machines setup
Insert the screenshot showing:
- Windows Server VM.
- Ubuntu VM.
- VMware Workstation lab layout.

### 3. Onboarding and log validation
Insert the screenshot showing:
- `Heartbeat` received from both machines.
- Log ingestion validation in Sentinel.

### 4. Windows `SecurityEvent` logs
Insert the screenshot showing:
- `SecurityEvent` table.
- Failed RDP logon events with Event ID `4625`.

### 5. Ubuntu `Syslog` logs
Insert the screenshot showing:
- `Syslog` table.
- `auth` / `authpriv` SSH-related entries.

### 6. Windows analytics rule
Insert the screenshot showing:
- The `Failed_RDP_login` analytics rule.
- Rule query and scheduling details.

### 7. Ubuntu analytics rule
Insert the screenshot showing:
- The SSH analytics rule.
- Query used to detect failed SSH logins.

### 8. Incident creation
Insert the screenshot showing:
- The generated incident in Microsoft Sentinel.
- Incident details and affected entities.

### 9. Outlook alert email
Insert the screenshot showing:
- The alert email received in Outlook.
- Incident information included in the message.

### 10. Playbook / automation
Insert the screenshot showing:
- The playbook in Logic Apps.
- The automation flow used for response.

### 11. IP blocking result
Insert the screenshot showing:
- The blocked IP action.
- Firewall rule or automation confirmation after 10 failed attempts.

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

Created by: **[Your Name]**  
Cybersecurity Engineering Student  
Focused on SIEM, SOC operations, cloud security, and network security.

## License

This project is shared for educational and lab purposes.
