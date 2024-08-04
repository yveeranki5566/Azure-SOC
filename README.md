# Building a SOC + Honeynet in Azure (Live Traffic)
![image](https://github.com/user-attachments/assets/dd942c7c-d24f-41da-9ae3-4eac3abcbe68)
## Introduction
In this project, I built a small honeynet in Azure and ingested logs from various resources like Virtual machines, storage accounts, key vaults and sql database into a Log Analytics workspace. This data is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I queried and documented some of the security metrics in an insecure environment for 24 hours, after that I have applied some security controls to harden the environment, and again documented the metrics for another 24 hours period. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)

**The architecture of the mini honeynet in Azure consists of the following components:**

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel
  
## Step by step implementation
**Step1. Created resource group, virtual network and virtual machines along with an attack VM and attack virtual network.**
<img width="1375" alt="image" src="https://github.com/user-attachments/assets/a5aee3d2-8095-42a9-8e8d-5c86da00ecd5">
<img width="1091" alt="image" src="https://github.com/user-attachments/assets/72bddd9a-b984-4a0d-9243-0d87ab1b03d6">
<img width="1328" alt="image" src="https://github.com/user-attachments/assets/bedc97cb-89f3-4d80-a72d-f5c1630c16d1">

**Step2. Next, configure NSG's to allow malicious traffic into the network.**
<img width="1825" alt="image" src="https://github.com/user-attachments/assets/b34f567e-6295-4f0b-bac2-af4f5fbe5112">
<img width="1802" alt="image" src="https://github.com/user-attachments/assets/c62cec8b-414e-48ba-8a8d-37c4432b1c47">

**Step3:Install Sql server on windows VM and make the VM vulnerable.**
![image](https://github.com/user-attachments/assets/fc630f9c-3a47-416e-8684-61eb513e0b20)

![image](https://github.com/user-attachments/assets/d35dfc1c-e4b4-4a32-b3e2-6e833d4d03e8)

**Step4:Login to attack VM and try to login to sql server with wrong password and check for the failed logs in Windows Event Viewer.**
![image](https://github.com/user-attachments/assets/a39740f9-1eef-4562-8161-a0f465d3a47d)

![image](https://github.com/user-attachments/assets/0e93ee54-49a8-4feb-b98c-02dea4ef3bce)

**Step5:Create Azure active directory, users and assign different roles.**

<img width="776" alt="image" src="https://github.com/user-attachments/assets/3dbc6543-2f68-407f-a79d-25cf5456bce9">

<img width="814" alt="image" src="https://github.com/user-attachments/assets/3a8b3429-2ec5-4033-817b-a0e7ecf09481">

**Step5:Log Analytics and Microsoft Sentinel setup.**
Create Log analytics workspace and microsoft sentinel.

<img width="1069" alt="image" src="https://github.com/user-attachments/assets/26521336-0f17-4457-8146-b9805c10e200">

<img width="1261" alt="image" src="https://github.com/user-attachments/assets/5d0bb837-ce73-4c76-9601-b1838644559b">

Inside the Watchlist wizard, enter geo_ipv4,created azure storage and Blob SAS URL.

<img width="1193" alt="image" src="https://github.com/user-attachments/assets/309f85f8-1f4d-437d-bdc7-a7bc93debad3">

Create flow logs for both Windows and Linux VM

<img width="1183" alt="image" src="https://github.com/user-attachments/assets/2b1672b7-d59a-4c5c-ae27-834fc1415154">

<img width="1083" alt="image" src="https://github.com/user-attachments/assets/1d75ed45-7f91-48db-bc54-a0c3ddba3237">

Next Enable diagnostic Settings for both Windows-Vm and Linux-Vm Network Security Groups.

<img width="776" alt="image" src="https://github.com/user-attachments/assets/36131163-e477-4a98-a58e-c5010c2401b6">

configure data collection rules within our Log Analytics Workspace.

<img width="1141" alt="image" src="https://github.com/user-attachments/assets/8d88dfe0-0fab-4be7-bdd3-835a17699c99">

**Step6:Creating Diagnostic Setting to ingest Azure Active Directory (AAD) Logs.**

<img width="844" alt="image" src="https://github.com/user-attachments/assets/6fd39326-75b7-4549-aa3c-d95984e9f1df">

**Step7:Export Azure Activity Logs to Log Analytics Workspace.**
<img width="1533" alt="image" src="https://github.com/user-attachments/assets/d72dc646-ba64-41f6-a4cf-3922de756ee5">

**Step8:Configure Logging for Azure Blob Storage & Key Vault.**
Blob Setting:

<img width="1220" alt="image" src="https://github.com/user-attachments/assets/3fe3270b-f045-43a6-828b-32374776c3ef">

<img width="797" alt="image" src="https://github.com/user-attachments/assets/3c7ddf47-c467-45fc-a357-2f819175313f">

Keyvault setting:

<img width="788" alt="image" src="https://github.com/user-attachments/assets/4cdf9f2f-bf68-4705-83b3-56f2dfcd03d9">

**Step9:Create Microsoft Sentinel Workbooks that show different types of malicious traffic from around the world, targeting our resources.**

<img width="1268" alt="image" src="https://github.com/user-attachments/assets/23263507-62f1-4cf2-8ecc-cfeaf516fe6a">

**Step10:Create Custom Analytic Rules to Generate Security Alerts For Incident Reponses Practice.**

<img width="1631" alt="image" src="https://github.com/user-attachments/assets/71871ab0-ef15-4935-9366-1ec54ba1ab0a">

Lets Query and check for the metrics in Log analytics workspace.

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2024-08-02 14:59:11
Stop Time 2024-08-03  4:59:11

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 28412
| Syslog                   | 3850
| SecurityAlert            | 1
| SecurityIncident         | 186


## Attack Maps Before Hardening / Security Controls

<img width="1556" alt="linuxssh" src="https://github.com/user-attachments/assets/1746657f-a980-41bd-a2f4-ae31a9df21d8">

<img width="1330" alt="mssql " src="https://github.com/user-attachments/assets/c544f826-f137-483c-a1cd-f02dc2145cde">

<img width="1418" alt="windows ssh" src="https://github.com/user-attachments/assets/391ffcb3-4136-4766-a386-44133ad8feb3">

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint.


## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2024-08-03 6:59:11
Stop Time 2024-08-04  6:59:11

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 7890
| Syslog                   | 680
| SecurityAlert            | 0
| SecurityIncident         | 0

## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.














 










