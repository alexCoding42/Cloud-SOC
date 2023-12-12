# Building a SOC and Honeynet in Azure (Live Traffic)
![Cover](https://www.dropbox.com/s/x7gdmk9lbaez42x/cover.png?raw=1)

## Introduction

In this project, I built a honeynet in Azure to deliberately attract malicious actors from around the world and made them attack my honeynet. Then I created attack maps from all the incidents and practiced incident response. Finally, I hardened my honeynet to reduce the attacks and compared the metrics between the insecure environment and the secure environment.

## Steps

I will explain in details each steps that I took to build this project:

1. Created a Network Security Group in Azure to gather all my resources. The resources were the following:
  - 3 Virtual Machines: Windows 10 (victim), Windows 10 (attacker), Ubuntu 22.04, MS SQL Server installed on the Windows 10 victim VM.
  - Enabling Microsoft Sentinel.
  - Enabling Microsoft Defender for Cloud.
  - Creating one Log Analytics Workspace.
  - Creating Azure Storage account.
  - Creating Azure Key Vault and adding secrets.

2. Allowed all inbound traffic to the Virtual Machines, disabled the firewalls to make my environment very insecure.
3. Enabled Azure Active Directory and assigned different roles and access controls to my Network Security Group.
4. Created a Log Analytics Workspace in Microsoft Sentinel to monitor logs and ingested a geoip json file to build attack maps later.
5. Enabled Microsoft Defender for Cloud and logs collection from all Virtual Machines.
6. Enabled logging from MS SQL Server and transfer these logs to Windows Event Viewer (for them to be able to be retrieve by Microsoft Sentinel).
7. Configured Azure Key Vault and added secrets.
8. Configured Azure Storage and added assets.
9. Built Workbooks in Microsoft Sentinel to create attack maps and KQL queries.
10. Created analytics rules in Microsoft Sentinel to be able to detect and create incidents automatically.
11. Capture the traffic during 24 hours in the insecure environment.
12. Analysed incidents by severity, tried to gather as much information about them, and tried to closed false positive incidents by documenting each one.
13. Practiced incident response by working on incidents and hardened the environment according to the NIST 800-53 framework recommendations.
14. Captured the traffic during 24 hours in the secure environment and compared the metrics.

## Metrics

The metrics that will be showed during this project are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- NSG Inbound Malicious Flows Allowed (Malicious Flows allowed into our honeynet)

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening Environment / Applying Some Security Controls

> [!NOTE]
> This attack map shows the traffic involved in a Network Security Group when all inbound traffic is allowed.

![NSG inbound map](https://www.dropbox.com/scl/fi/iin637sg0ztxektgk84i3/nsg-allow-malicious-inbound-flows.png?rlkey=l9ve4btccyevrnl2h1e9zxaq6&raw=1)

---

> [!NOTE]
> This attack map shows all the failed connection attempts on the Linux Virtual Machine.

![Linux attacks map](https://www.dropbox.com/scl/fi/tj8oyseg87f1uhm006st0/linux-ssh-authentication-failures.png?rlkey=azt8t7qfq3riqzlidwc53p73f&raw=1)

---

> [!NOTE]
> This attack map shows all the failed connection attempts on the Windows Virtual Machine.

![Windows attacks map](https://www.dropbox.com/scl/fi/a9gxvl752gkc3kwrsn230/windows-rdp-smb-authentication-failures.png?rlkey=1xz6d783l2flgxkefenu3m28p&raw=1)

---

> [!NOTE]
> This attack map shows all the failed connection attempts on the MS SQL Server.

![MS SQL attacks map](https://www.dropbox.com/scl/fi/qyal8s5fn56u6aupzb92d/ms-sql-server-authentication-failures.png?rlkey=zj735usgyaarvzwdls8p5a9ec&raw=1)

## Metrics Before Hardening Environment / Applying Some Security Controls

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

## Attack Maps After Hardening Environment / Applying Some Security Controls

> [!IMPORTANT]
> Some map queries returned no results due to no instances of malicious activity for the 24 hour period after hardening.

> [!NOTE]
> This attack map shows the traffic involved in a Network Security Group when some security controls are applied.

![NSG security controls map](https://www.dropbox.com/scl/fi/0mzx9poazcmiss2zzg0hj/NSG-Allowed-Malicious-Inbound-Flows-Map-After-Hardening-Controls.png?rlkey=7t65ob7cm501w6u13gcsn3awy&raw=1)

---

> [!NOTE]
> This attack map shows all the failed connection attempts on the Linux Virtual Machine when some security controls are applied.

![Linux attacks map](https://www.dropbox.com/scl/fi/31c02kltlgc164ebyvact/Linux-SSH-Authentication-Failures-After-Hardening-Controls.png?rlkey=zo8odmx3yu00ueaqte3wq7ylx&raw=1)

---

> [!NOTE]
> This attack map shows all the failed connection attempts on the Windows Virtual Machine when some security controls are applied.

![Windows attacks map](https://www.dropbox.com/scl/fi/fy125zpf45217h2jgtuek/Windows-RDP-SMB-Authentication-Failures-After-Hardening-Controls.png?rlkey=wjsdr9wrjvlvx1xh7p2cticl6&raw=1)

---

> [!NOTE]
> This attack map shows all the failed connection attempts on the MS SQL Server when some security controls are applied.

![MS SQL attacks map](https://www.dropbox.com/scl/fi/hdhatn2l1rccoup9ic465/MS-SQL-Server-Authentication-Failures-After-hardening-Controls.png?rlkey=8ecc4h1qyejub6asqr9kc8qvt&raw=1)

## Metrics After Hardening Environment / Applying Security Controls

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

## Metrics Comparing Before And After Hardening Environment / Applying Some Security Controls

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
