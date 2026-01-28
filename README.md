## 📋 Project Overview
[cite_start]This project documents the Phase 1 implementation for the "Expanding Horizons" network infrastructure[cite: 52]. [cite_start]The objective was to deploy a secure Windows Server environment featuring Active Directory, DNS, Web Services (IIS), Secure FTP, and RADIUS/NPS authentication, all protected behind an OPNsense firewall[cite: 59, 78].

## 🛠️ Network Architecture
[cite_start]The network uses a centralized logical topology with OPNsense acting as the Gateway and Firewall[cite: 56].

**IP Addressing Scheme:**

| Device | IP Address | Role/Notes |
| :--- | :--- | :--- |
| **OPNsense** | `192.168.1.1` | [cite_start]Gateway / Firewall [cite: 59] |
| **DC01** | `192.168.1.2` | [cite_start]Primary DC, DNS, DHCP [cite: 59] |
| **DC-BKP** | `192.168.1.3` | [cite_start]Backup DC, DNS Replica [cite: 59] |
| **appsrv01** | `192.168.1.161` | [cite_start]Application Server (IIS, FTP, NPS) [cite: 59] |
| **Win10 Client** | `192.168.1.45` | [cite_start]DHCP Client [cite: 59] |
| **Win11 Client** | `192.168.1.100` | [cite_start]DHCP Client [cite: 59] |

* [cite_start]**DHCP Range:** `192.168.1.41 - 192.168.1.245`[cite: 60].
* [cite_start]**DNS Forwarders:** `1.1.1.1, 8.8.8.8`[cite: 60].

![Logical Network Diagram](images/topology.png)
*(Note: Upload the diagram from section 1 here)*

## 🔐 Active Directory Design
[cite_start]The `oscar.net` domain is structured into Organizational Units (OUs) to separate management and operations[cite: 62, 63]:

* [cite_start]**IT MANAGERS:** Full access to IT resources[cite: 66].
* [cite_start]**EMPLOYEES:** Access to management files and reports[cite: 67].

**Users and Security Groups:**

| Username | Name | Department | OU | Role |
| :--- | :--- | :--- | :--- | :--- |
| **Matias** | Matias | IT MANAGERS | IT MANAGERS | [cite_start]Domain Admin [cite: 69] |
| **Cr7** | Ronaldo | IT MANAGERS | IT MANAGERS | [cite_start]General Manager [cite: 69] |
| **MS10** | Messi | Sales | EMPLOYEES | [cite_start]Sales User [cite: 69] |
| **Jjose** | Jose | Sales | EMPLOYEES | [cite_start]Sales User [cite: 69] |
| **Daniel** | Dani | Sales | EMPLOYEES | [cite_start]Sales User [cite: 69] |

* [cite_start]**Security Group:** `SG_SecureFTP_Users` (Members: Matias, Ronaldo)[cite: 71, 72, 73].

## ⚙️ Services Implemented

### 1. Web Server (IIS)
[cite_start]An internal portal was deployed, accessible via `http://oscar.oscar.net`[cite: 102].
![IIS Portal](images/iis_portal.png)

### 2. Secure FTP & FSRM
* [cite_start]**Access Control:** Restricted strictly to the `SG_SecureFTP_Users` group[cite: 71].
* [cite_start]**FSRM Policies:** Uploading `.exe` files is blocked [cite: 108][cite_start], and storage quotas are enforced[cite: 109].
* [cite_start]**Verification:** Unauthorized users (Jose, Daniel) are denied access[cite: 106].

![FTP Access Denied](images/ftp_access_denied.png)

### 3. RADIUS / NPS
Centralized authentication was configured using NPS. [cite_start]Access tests from OPNsense confirmed success for Admins and failure for standard users[cite: 111, 112].
![RADIUS Test](images/radius_test.png)

### 4. Group Policy Objects (GPO)
[cite_start]The following GPOs were applied and verified using `gpresult /r`[cite: 80, 114]:
* **Windows Firewall Logging:** Enables logging on servers for traffic monitoring.
* **Common Drive Mapping:** Automatically maps shared folders on `appsrv01`.

## 🛡️ Backup Strategy
[cite_start]Daily scheduled tasks were configured using **Windows Server Backup**[cite: 91]:
* [cite_start]**Primary DC:** System State backup to `DC-BKP`[cite: 89].
* [cite_start]**appsrv01:** System State + C: Root backup to `DC-BKP`[cite: 90].
* [cite_start]**Verification:** Backups confirmed as "Successful" on the backup share[cite: 121, 122].

![Backup Verification](images/backup_verify.png)

---
*Educational Project - Infrastructure Security*
