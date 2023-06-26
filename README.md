# Building a SOC + Honeynet in Azure (Live Traffic)

## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics that will be showed are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture
The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
**This attack map shows the traffic involved in a Network Security Group when all inbound traffic is allowed**
![NSG inbound map](https://www.dropbox.com/s/vup18u8ue8h0p7u/nsg-malicious-allowed-in.png?raw=1)

**This attack map shows all the failed connection attempts on the Linux Virtual Machine**
![Linux attacks map](https://www.dropbox.com/s/qdwhn8ds6hkg79d/syslog-ssh-auth-fail.png?raw=1)

**This attack map shows all the failed connection attempts on the Windows Virtual Machine**
![Windows attacks map](https://www.dropbox.com/s/6yyb9p6h3m0g1d2/windows-rdp-smb-auth-fail.png?raw=1)

**This attack map shows all the failed connection attempts on the MS SQL Server**
<img width="972" alt="mssql-auth-fail" src="https://github.com/alexCoding42/Cloud-SOC/assets/56698920/b36a4f83-3352-4269-b4e6-a88829e6150a">

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-06-11 12:03
Stop Time 2023-06-12 12:03

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 3874
| Syslog                   | 122
| SecurityAlert            | 0
| SecurityIncident         | 135
| AzureNetworkAnalytics_CL | 25

## Attack Maps After Hardening / Security Controls

Many map queries returned no results due to no instances of malicious activity for the 24 hour period after hardening.

**This attack map shows the traffic involved in a Network Security Group when some security controls are applied and hardened**
![NSG security controls map](https://www.dropbox.com/s/kf5qzy7dcs63ubr/%28after%29-nsg-malicious-allowed-in.png?raw=1)

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-06-13 18:38
Stop Time	2023-06-14 18:38

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 521
| Syslog                   | 41
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Metrics Comparing Before And After Hardening / Security Controls

The following table compare metrics measured in our environment before and after we have applied security controls:

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | -86.5%
| Syslog                   | -66.4%
| SecurityAlert            | 0
| SecurityIncident         | -100%
| AzureNetworkAnalytics_CL | -100%

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
