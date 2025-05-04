# ☁️ Azure Cloud Platform - Hybrid University

This document outlines the detailed setup and usage of Microsoft Azure services within **Hybrid University**, focusing on hybrid connectivity, infrastructure planning, identity management, device management, backup strategy, and licensing.

---

## 🔰 Purpose 

Hybrid University leverages Azure primarily to manage:

- Identity and access control using Azure Active Directory (Azure AD)
- Seamless collaboration and productivity through Microsoft 365
- Device security and compliance via Microsoft Intune
- Data protection using Azure Backup
  
---

## 🧩 Key Azure Components Used

| Service            | Role in EduTech Infrastructure                                |
|--------------------|---------------------------------------------------------------|
| **Azure Active Directory** | Centralized identity management, SSO, MFA, conditional access |
| **Microsoft 365**          | Cloud productivity apps (Outlook, OneDrive, Teams, SharePoint) |
| **Intune**                 | Endpoint security and mobile device management (MDM/MAM)       |
| **Azure Backup**           | Cloud backup for critical devices and virtual machines         |

---

## 🏛️ Architectural Overview

```plaintext
                          +-----------------------------+
                          |     Microsoft Azure AD      |
                          |     (Identity Provider)     |
                          +-------------+---------------+
                                        |
                        +---------------+----------------+
                        |     Azure AD Join / Intune      |
                        |  (Windows 10/11 Workgroup PCs)  |
                        +---------------+----------------+
                                        |
            +---------------------------+----------------------------+
            |            Microsoft 365 Business/Education            |
            | (Exchange Online, SharePoint, Teams, OneDrive, etc.)  |
            +---------------------------+----------------------------+
                                        |
                          +-------------+--------------+
                          |        Azure Backup        |
                          |  (Data Protection Layer)   |
                          +----------------------------+

---

## 🏗️ Azure Hybrid Connectivity Setup

Hybrid University operates in a **cloud-first hybrid model**, relying on Microsoft Azure services for central identity and security, while supporting Windows workgroup devices.

### 🔌 VPN and Connectivity:

| Component            | Description                                                                |
| -------------------- | -------------------------------------------------------------------------- |
| VPN Type             | Site-to-Site VPN (between university ISP and Azure Virtual Network)        |
| VPN Gateway SKU      | Basic / VpnGw1 (Free tier or pay-as-you-go depending on traffic)           |
| DNS Forwarding       | Internal Azure DNS + On-prem DNS forwarding (if needed for legacy devices) |
| Local Gateway IP     | Provided by local ISP router/Firewall (FortiGate/Cisco)                    |
| Azure Gateway Subnet | `10.0.255.0/27` (separate subnet for VPN GW)                               |
| Virtual Network CIDR | `10.0.0.0/16`                                                              |

---

## 🌐 Azure Networking and IP Addressing Plan

```plaintext
Azure VNet (10.0.0.0/16)
├── Subnet-Identity       : 10.0.1.0/24 (Azure AD, Identity services)
├── Subnet-Backup         : 10.0.2.0/24 (Azure Recovery Services vault)
├── Subnet-Intune         : 10.0.3.0/24 (Device onboarding, policies)
├── Subnet-VPNGateway     : 10.0.255.0/27
```

---

## 📦 Microsoft Office 365 Configuration Setup

### Core Services:

* Exchange Online (email, calendar)
* SharePoint Online (file server, team sites)
* OneDrive (personal cloud storage)
* Microsoft Teams (virtual classes, meetings)

### Tenant Setup:

| Setting                       | Value                                           |
| ----------------------------- | ----------------------------------------------- |
| Primary Domain                | `hybriduniversity.edu`                          |
| User Creation                 | Manual / CSV Upload (Azure Portal)              |
| MFA                           | Enabled (Admin + Faculty accounts)              |
| Email Routing                 | Direct via Exchange Online                      |
| External Sharing (SharePoint) | Disabled for students                           |
| DKIM & SPF                    | Configured via DNS (GoDaddy or other registrar) |

---

## 🧠 Azure AD Sample Structure

```plaintext
HybridUniversity.onmicrosoft.com
├── Users
│   ├── Students
│   ├── Faculty
│   └── AdminStaff
├── Groups
│   ├── Dept-IT
│   ├── Dept-Science
│   ├── Dept-Commerce
│   └── Dept-Admin
├── Devices
│   ├── Azure AD Registered PCs
│   └── Intune Managed PCs
```

* SSO enabled for Microsoft 365, Zoom, and web apps.
* Groups used for SharePoint and Teams access control.

---

## 📱 Intune Management Table

| Feature             | Status                           |
| ------------------- | -------------------------------- |
| Device Enrollment   | Manual + Bulk Enrollment         |
| OS Support          | Windows 10/11 (non-domain)       |
| Compliance Policies | Configured (Password, BitLocker) |
| App Deployment      | Microsoft Apps + Zoom            |
| Device Restriction  | USB, Screen Timeout              |
| Reporting           | Enabled                          |

---

## 💾 Azure Backup Plans and Strategy

| Target Group     | Tool Used            | Frequency | Retention | Storage Tier        |
| ---------------- | -------------------- | --------- | --------- | ------------------- |
| Admin Laptops    | Azure Backup Agent   | Daily     | 90 Days   | Locally Redundant   |
| Shared VM (Labs) | Azure Recovery Vault | Daily     | 30 Days   | Geo-Redundant       |
| Student Devices  | OneDrive             | Auto Sync | 30 Days   | Microsoft 365 Cloud |

Backups are configured through the Azure Recovery Services Vault in the `Subnet-Backup`.

---

## 📊 License Count Table

| License Type                 | Assigned To          | Quantity | Source              |
| ---------------------------- | -------------------- | -------- | ------------------- |
| Microsoft 365 Business Basic | Admin + Faculty      | 50       | Microsoft CSP       |
| Microsoft 365 A1             | Students             | 500      | Microsoft Education |
| Azure AD Free                | All users (identity) | 550      | Azure Free Tier     |
| Intune for Education (trial) | Faculty Devices      | 25       | Trial/Promo         |

---

* Admin Access: [portal.azure.com](https://portal.azure.com)
