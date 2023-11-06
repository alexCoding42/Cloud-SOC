<img width="1727" alt="MS-SQL-Server-Authentication-Failures-After-hardening-Controls" src="https://github.com/alexCoding42/Cloud-SOC-Honeynet/assets/56698920/63c2c05a-b30b-4604-9a78-ccb9e5d7be28"># Building a SOC + Honeynet in Azure (Live Traffic)
![Cover](https://www.dropbox.com/s/x7gdmk9lbaez42x/cover.png?raw=1)

## Introduction

In this project, I built a Honeynet in Azure to attract malicious actors from around the world. For that purpose, I deliberately built insecure Windows, Linux virtual machines and SQL server and gathered their logs. Then I deployed a SOC to ingest these logs into a Log Analytics workspace. I set up SIEM alerts using KQL queries by aggregating these logs and used Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. To address these SIEM alerts I acted like an incident response team to analyze each alert and close them while documenting the process. Finally, to harden the insecure honeynet I used Microsoft Defender Cloud regulatory compliance which provided security controls and best practices to align with NIST 800-53 framework; I implemented some security recommendations to protect my network resources groups. I deployed Azure Private Link and Firewall for Azure Key Vault and Storage Account instances. To compare the results between both insecure and secure environments, I measured some security metrics in the insecure environment for 24 hours, and measured metrics for another 24 hours after applying the security controls. The metrics that will be showed are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- NSG Inbound Malicious Flows Allowed (Malicious Flows allowed into our honeynet)

## Architecture
The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (1 Windows 10, 1 MS SQL Server on Windows 10, 1 Linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
**This attack map shows the traffic involved in a Network Security Group when all inbound traffic is allowed**
![NSG inbound map](https://www.dropbox.com/scl/fi/iin637sg0ztxektgk84i3/nsg-allow-malicious-inbound-flows.png?rlkey=l9ve4btccyevrnl2h1e9zxaq6&raw=1)

**This attack map shows all the failed connection attempts on the Linux Virtual Machine**
![Linux attacks map](https://www.dropbox.com/scl/fi/tj8oyseg87f1uhm006st0/linux-ssh-authentication-failures.png?rlkey=azt8t7qfq3riqzlidwc53p73f&raw=1)

**This attack map shows all the failed connection attempts on the Windows Virtual Machine**
![Windows attacks map](https://www.dropbox.com/scl/fi/a9gxvl752gkc3kwrsn230/windows-rdp-smb-authentication-failures.png?rlkey=1xz6d783l2flgxkefenu3m28p&raw=1)

**This attack map shows all the failed connection attempts on the MS SQL Server**
![MS SQL attacks map](https://www.dropbox.com/scl/fi/qyal8s5fn56u6aupzb92d/ms-sql-server-authentication-failures.png?rlkey=zj735usgyaarvzwdls8p5a9ec&raw=1)

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
- Start Time 2023-10-04 18:10pm
- Stop Time 2023-10-05 18:10pm

| Metric                                         | Count
| -----------------------------------------------| -----
| SecurityEvent (Windows VMs)                    | 17606
| Syslog (Linux VM)                              | 3510
| SecurityAlert (Microsoft Defender for Cloud)   | 131
| SecurityIncident (Sentinel Incidents)          | 136
| NSG Inbound Malicious Flows Allowed            | 4223

## Attack Maps After Hardening / Security Controls

Some map queries returned no results due to no instances of malicious activity for the 24 hour period after hardening.

**This attack map shows the traffic involved in a Network Security Group when some security controls are applied**
![NSG security controls map](https://www.dropbox.com/scl/fi/0mzx9poazcmiss2zzg0hj/NSG-Allowed-Malicious-Inbound-Flows-Map-After-Hardening-Controls.png?rlkey=7t65ob7cm501w6u13gcsn3awy&raw=1)

**This attack map shows all the failed connection attempts on the Linux Virtual Machine when some security controls are applied**
![Linux attacks map](https://www.dropbox.com/scl/fi/31c02kltlgc164ebyvact/Linux-SSH-Authentication-Failures-After-Hardening-Controls.png?rlkey=zo8odmx3yu00ueaqte3wq7ylx&raw=1)

**This attack map shows all the failed connection attempts on the Windows Virtual Machine when some security controls are applied**
![Windows attacks map](https://www.dropbox.com/scl/fi/fy125zpf45217h2jgtuek/Windows-RDP-SMB-Authentication-Failures-After-Hardening-Controls.png?rlkey=wjsdr9wrjvlvx1xh7p2cticl6&raw=1)

**This attack map shows all the failed connection attempts on the MS SQL Server when some security controls are applied**
![MS SQL attacks map](https://www.dropbox.com/scl/fi/hdhatn2l1rccoup9ic465/MS-SQL-Server-Authentication-Failures-After-hardening-Controls.png?rlkey=8ecc4h1qyejub6asqr9kc8qvt&raw=1)

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
- Start Time 2023-10-10 10:06am
- Stop Time	2023-10-11 10:06am

| Metric                                         | Count
| -----------------------------------------------| -----
| SecurityEvent (Windows VMs)                    | 22149
| Syslog (Linux VM)                              | 4
| SecurityAlert (Microsoft Defender for Cloud)   | 0
| SecurityIncident (Sentinel Incidents)          | 0
| NSG Inbound Malicious Flows Allowed            | 0

## Metrics Comparing Before And After Hardening / Security Controls

The following table compare metrics measured in our environment before and after we have applied security controls:

| Metric                                         | Count
| -----------------------------------------------| -----
| SecurityEvent (Windows VMs)                    | 25.80%
| Syslog (Linux VM)                              | -99.89%
| SecurityAlert (Microsoft Defender for Cloud)   | -100%
| SecurityIncident (Sentinel Incidents)          | -100%
| NSG Inbound Malicious Flows Allowed            | -100%

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
