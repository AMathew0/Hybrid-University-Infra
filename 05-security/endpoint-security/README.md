# 🔐 Endpoint Security & Protection – Hybrid University

This section outlines the endpoint security strategy employed at Hybrid University to protect user devices, data, and communications across its distributed and hybrid infrastructure. The organization enforces strong security baselines across all endpoints using centralized tools and best-in-class open-source and commercial security solutions.

---

## 🧱 Security Component Stack Overview

| **Security Component** | **Tool / Platform**                                | **Purpose**                                                 |
|------------------------|-----------------------------------------------------|-------------------------------------------------------------|
| **Antivirus / EDR**    | Microsoft Defender for Endpoint (via Intune)        | Real-time protection, threat detection, response automation |
| **Drive Encryption**   | BitLocker (Windows), LUKS (Linux)                   | Full-disk encryption for data-at-rest security              |
| **Web Protection**     | FortiGuard DNS Filtering                            | Block malicious/phishing domains, enforce safe browsing     |
| **DLP & Email Security** | Microsoft Purview (DLP), Defender for Office 365  | Prevent data leakage and protect against phishing/spam      |
| **SSL/DNS Security**   | Let’s Encrypt (SSL), Cloudflare (DNS, CDN, DDoS)    | Secure external services, mitigate attacks                  |

---

## 🛡️ Antivirus & EDR: Microsoft Defender for Endpoint

Hybrid University uses **Microsoft Defender for Endpoint** integrated with **Microsoft Intune** to provide comprehensive threat protection and endpoint management across all Windows 10/11 and supported macOS/Linux devices.

### Features:
- Real-time virus/malware protection.
- Automated investigation and remediation (AIR).
- Endpoint detection & response (EDR) analytics and behavior monitoring.
- Integration with Microsoft 365 security ecosystem.
- Managed via **Intune Endpoint Security Policies**.

### Deployment:
- Enforced through **Microsoft Intune** for all university-managed Windows devices.
- Custom security baselines and alerts configured for high-risk activities.

---

## 🔒 Drive Encryption

To ensure data-at-rest protection, full disk encryption is enforced on all university-owned devices.

| **OS**       | **Encryption Tool** | **Enforcement**                        |
|--------------|---------------------|----------------------------------------|
| Windows 10/11| BitLocker           | Enabled via Intune MDM policy          |
| Linux        | LUKS                | Manually encrypted during provisioning |

- **BitLocker recovery keys** are securely stored in Azure AD and/or backed up to a secure admin vault.
- Regular checks ensure encryption compliance.

---

## 🌐 Web Protection: FortiGuard DNS Filtering

All user and staff devices are protected by **FortiGuard DNS filtering**, enforcing safe browsing policies and blocking access to malicious or inappropriate websites.

### Highlights:
- Cloud-managed DNS filter policies.
- Blocks malware/phishing domains, proxies, adult content, etc.
- Integrated with firewall and endpoint policies.
- Compatible with on-campus and remote access scenarios.

---

## 📧 Email Security & DLP

Microsoft 365-based services are protected by integrated security solutions:

### Microsoft Purview (Data Loss Prevention):
- Enforces policies to restrict sharing of sensitive data (e.g., credit card numbers, student IDs).
- Alerts admins when sensitive data is shared externally or unapproved.

### Microsoft Defender for Office 365:
- Phishing & malware protection for Exchange Online.
- Safe Links and Safe Attachments scanning.
- Real-time email threat intelligence and quarantine.

---

## 🌍 SSL & DNS Security

To ensure secure access to online services and public-facing portals, Hybrid University uses:

| **Service**          | **Tool/Provider**         | **Function**                        |
|----------------------|---------------------------|-------------------------------------|
| SSL Certificate Mgmt | Let’s Encrypt              | Auto-renewing TLS certificates      |
| DNS / CDN / DDoS     | Cloudflare                | Secure DNS, CDN, DDoS mitigation    |

### Implementation:
- All public web apps are protected with **Cloudflare Pro features** (WAF, DNSSEC, Bot Management).
- All hosted sites use **HTTPS** with free certificates via **Let’s Encrypt**, automatically renewed.
- **DNS** managed via Cloudflare ensures fast resolution, security (DNSSEC), and geo-performance.

---

## 🔐 Summary Table

| **Area**             | **Tool**                                | **Managed By**             | **Protection Type**                  |
|----------------------|-----------------------------------------|----------------------------|--------------------------------------|
| Endpoint EDR         | Microsoft Defender for Endpoint         | Intune                     | Threat detection & response          |
| Disk Encryption      | BitLocker / LUKS                        | Intune / Manual (Linux)    | Data-at-rest protection              |
| DNS Filtering        | FortiGuard DNS                          | Fortinet Cloud             | Web protection & access control      |
| Email Security       | Microsoft Defender for O365             | M365 Security & Compliance | Anti-spam, anti-phishing             |
| DLP                  | Microsoft Purview                       | Intune / Compliance Center | Prevent data leaks                   |
| SSL / DNS            | Let’s Encrypt / Cloudflare              | Web Admin Team             | Web security, DDoS protection        |

---

## ✅ Best Practices Enforced

- Devices must be compliant with encryption and antivirus policies to access corporate resources.
- Alerts and analytics from Defender and Fortinet feed into centralized monitoring (e.g., Microsoft Sentinel, Graylog).
- Devices connecting from outside must pass compliance checks via **Conditional Access Policies**.
- Guest and BYOD devices are isolated using VLANs and do not have access to internal resources.

---

## 📌 Future Enhancements

- Integration with **SIEM (Graylog / Sentinel)** for unified alerting.
- Endpoint patch automation using **Intune + Winget + PowerShell scripts**.
- BYOD containerization via **Microsoft Endpoint Manager** or **Mobile Application Management (MAM)**.
