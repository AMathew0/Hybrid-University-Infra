# 👥 Identity & Access Management (IAM) – Hybrid University

This document outlines the Identity and Access Management (IAM) architecture implemented at Hybrid University to ensure secure, role-based access across all systems, platforms, and services.

---

## 🎯 Objectives

- Enforce **Zero Trust principles** through strict identity verification.
- Provide **centralized identity management** for all users (faculty, staff, students).
- Implement **least privilege access** across cloud and on-premises resources.
- Enable **secure SSO** (Single Sign-On) and MFA (Multi-Factor Authentication).

---

## 🧰 Technologies Used

| Component               | Tool/Service                 | Purpose                                   |
|------------------------|------------------------------|-------------------------------------------|
| Identity Provider (IdP)| **Microsoft Entra ID (Azure AD)** | Primary user directory & SSO platform    |
| MFA                    | **Microsoft Authenticator**  | Second factor for login/auth              |
| Directory Services     | **Azure AD + SSSD (Linux)**  | Central auth for Windows/Linux endpoints  |
| Role-Based Access      | **Microsoft Entra Roles**    | Group-based access policies               |
| Conditional Access     | **Microsoft Entra CA**       | Enforce access policies per user/device   |

---

## 🧑‍🎓 User Roles & Groups

| Role             | Example Users                  | Access Scope                            |
|------------------|--------------------------------|------------------------------------------|
| IT Admins        | sysadmins@, infra@             | Global admin, Azure, firewall, servers   |
| Faculty Staff    | staff@, faculty@               | Teams, LMS, Exchange, shared drives      |
| Students         | student@                       | LMS, email, Teams                        |
| Developers       | dev@                           | GitHub, test environments, cloud services|

Groups are synchronized from Microsoft Entra ID to on-prem and cloud services via SCIM/API where supported.

## 🌐 Multi-Site AD Architecture

Hybrid University operates a **multi-site hybrid AD deployment**:

| Location             | Role                           | Description                                 |
|----------------------|----------------------------------|---------------------------------------------|
| Main Campus (On-Prem) | AD Forest Root                  | Primary AD domain (hybrid with Azure AD)    |
| Research Site (On-Prem) | Child Domain                   | Separate OU/Group policy structure          |
| Azure Cloud          | Azure AD + Entra ID             | Cloud-native directory, synced via AAD Connect |
| Google Cloud (GCP)   | Cloud Identity Federation       | Integrated with Azure AD SSO + SCIM         |
| Remote/Partner Sites | Azure AD B2B / Conditional Join | External user access via guest federation   |

---

## 🏛️ Active Directory Design – On-Premises

### 🗂️ Organizational Unit (OU) Structure

~~~markdown

HybridUniversity.local
├── FacultyOU
│ ├── Science
│ ├── Humanities
│ └── Engineering
├── StudentsOU
│ ├── UG
│ └── PG
├── ITAdminsOU
├── ResearchOU
│ ├── DataScience
│ └── BioInformatics
└── DevicesOU
├── CampusLaptops
├── LabComputers
└── Servers

~~~


### 👥 AD User & Group Strategy

| Group Type         | Naming Format         | Description                              |
|--------------------|-----------------------|------------------------------------------|
| Security Groups    | `SG-Faculty-Science`  | Used for ACLs, RBAC, Folder Permissions  |
| Distribution Lists | `DL-UG-Students2025`  | For email and notification use           |
| Dynamic Groups     | `DG-RemoteAccess-Users`| Conditional access based on attributes   |

---

## ☁️ Azure Active Directory (Entra ID)

### 🔗 Hybrid Identity via Azure AD Connect

- **Password hash sync + device write-back**
- **Group/OU filtering** to sync only targeted OUs
- **Device registration** (Hybrid Azure AD Join) enabled for domain-joined PCs

### 🔑 Azure AD Role Mapping

| Entra Role         | Assigned Group              | Purpose                              |
|--------------------|-----------------------------|--------------------------------------|
| Global Admin       | `SG-ITAdminsOU`             | Full tenant admin rights             |
| Application Admin | `SG-DevOps`                 | AWS/GCP integrations, app access     |
| Security Reader    | `SG-SOC-Auditors`           | View-only access to logs             |

### 🌐 Federation & SSO

| Platform       | Integration Type     | Status              |
|----------------|----------------------|---------------------|
| AWS IAM        | Entra SAML SSO       | Enforced with MFA   |
| Zoom           | Entra SAML SSO       | All users           |
| GitHub         | Azure OIDC           | Developer access    |
| Moodle LMS     | SAML via Azure       | Students/Faculty    |
| GCP Console    | SCIM + SAML          | Synced from Entra   |

---

## 🔒 MFA & Conditional Access

### MFA Enforced

| Group                 | Method                  | Notes                          |
|-----------------------|-------------------------|--------------------------------|
| `SG-ITAdminsOU`       | App + PIN               | Enforced for all logins        |
| `SG-Faculty-All`      | App Notification        | Required off-campus            |
| `SG-Students`         | Not Required            | Phase 2 rollout planned        |

### Conditional Access Rules

- **IP-based Blocking**: Geo-restriction enabled for high-risk countries
- **Compliant Devices Only**: Required for sensitive services (SharePoint Admin, AWS)
- **Risk-Based Access**: Medium/high-risk logins blocked or re-authenticated

---

## 🧩 Google Cloud IAM (Federated)

### 🔄 Identity Federation with Azure AD

- GCP Identity linked to **Azure AD via SAML**
- Groups from Azure auto-provisioned in GCP via **SCIM**
- MFA required before launching GCP services (Cloud Functions, BigQuery)

---

## 🔄 Device Join Types

| Device Type      | Join Type               | Platform                 |
|------------------|-------------------------|--------------------------|
| Windows Laptops  | Hybrid Azure AD Join    | On-prem + Entra ID       |
| Linux Laptops    | SSSD + AD Bind          | AD + PAM/SSO             |
| BYOD Devices     | Azure AD Registered     | Conditional Access       |
| Android/iOS      | Intune MAM + App Auth   | Limited container access |

---

## 🔍 Audit & Logging

- **Microsoft Entra ID** sign-in logs, risky sign-in alerts
- **On-prem AD** logs centralized to **Graylog/Wazuh**
- **SSO events** tracked per provider (Zoom, AWS, GitHub)
- All logs forwarded to **SIEM (Wazuh + Sentinel)**

---

## 🛡️ IAM Hardening Best Practices

- OU-based delegation to avoid overprivilege
- No standing global admin accounts – use **PIM time-bound elevation**
- External guest users reviewed monthly
- Dormant student accounts auto-expired after 180 days
- Daily AAD Connect health reporting + alerting

---

## ✅ Summary Table

| Component              | Solution                     | Managed By         |
|------------------------|------------------------------|--------------------|
| Central Directory      | On-prem AD + Azure AD        | IT Infrastructure  |
| Cloud Identity         | Azure Entra ID + SCIM        | Cloud Ops          |
| SSO Federation         | Azure AD (SAML/OIDC)         | IAM Team           |
| MFA Enforcement        | Azure AD + Authenticator     | Security Team      |
| Guest/External Access  | Azure AD B2B                 | Department Heads   |
| Audit Logs             | Graylog + Microsoft Sentinel | SOC                |

---

## 📌 Conclusion

Hybrid University’s IAM architecture leverages a hybrid AD setup with cloud identity federation, offering centralized control, secure SSO, granular access policies, and compliance-ready auditing. This unified model ensures all users—across multiple campuses and platforms—have **just the right access at just the right time**.

## 🔐 Authentication Policies

### 🌍 Single Sign-On (SSO)

- Implemented for:
  - Microsoft 365 (Exchange, Teams, SharePoint)
  - Zoom
  - AWS Console
  - GitHub
  - Custom LMS portal
- Backed by **SAML2 / OAuth2** via Microsoft Entra ID.

### 🔒 Multi-Factor Authentication (MFA)

| User Type    | MFA Method              | Enforced Location      |
|--------------|-------------------------|-------------------------|
| IT Admins    | Authenticator App + PIN | All logins, all IPs     |
| Faculty/Staff| App Notification        | Required off-campus     |
| Students     | Not Enforced            | Planned (Phase 2)       |

### 📜 Conditional Access Policies

- **Location-based access** (e.g., block from high-risk countries)
- **Device compliance** required for access to sensitive services
- **Session controls** for external devices (read-only access)

---

## 🔄 Integration Points

| Service            | Authentication Method     | IAM Integration          |
|--------------------|---------------------------|--------------------------|
| Windows Devices    | Azure AD Join + Intune    | Enforced via AutoPilot   |
| Linux Devices      | SSSD + Azure AD (via RHEL connector) | Role-based SUDO & access |
| AWS                | SSO via Microsoft Entra   | Federated access         |
| GitHub             | OIDC + Azure SSO          | Group access via team sync|
| Zoom               | SAML SSO (Azure)          | Staff & Student roles    |

---

## 🔍 Monitoring & Auditing

- **Sign-in logs** and alerts in Microsoft Entra ID portal
- **User Risk Events** (e.g., unfamiliar sign-in) surfaced in Entra Risk Reports
- Monthly access reviews for high-privilege accounts via **Privileged Identity Management (PIM)**
- Audit trails integrated with **Microsoft Sentinel** and **Wazuh**

---

## 📦 Backup & Recovery

| Scenario                     | Response                                       |
|-----------------------------|------------------------------------------------|
| Account compromise           | Immediate MFA reset + token revocation        |
| Password loss (student)      | Self-service password reset via Entra         |
| Admin access loss            | Break-glass accounts with monitored usage     |

---

## 🔐 Best Practices Enforced

- Strong password policies (min length + complexity)
- Role-based access control (RBAC) only
- Admin accounts separated from regular use
- Time-bound admin elevation (via PIM)
- Alerts on impossible travel or concurrent logins

---

## ✅ Summary

| Feature              | Solution Implemented          |
|----------------------|-------------------------------|
| Identity Directory   | Microsoft Entra ID (Azure AD) |
| SSO                  | Microsoft SSO (SAML/OAuth2)   |
| MFA                  | Microsoft Authenticator       |
| Role Management      | Azure AD Roles & Groups       |
| Conditional Access   | Entra Conditional Access      |
| Audit Logging        | Microsoft Sentinel + Wazuh    |

---

## 📎 Conclusion

Hybrid University uses a comprehensive IAM strategy built around Microsoft Entra ID to enable secure, scalable, and intelligent access control across all services. With SSO, MFA, role-based access, and detailed auditing, we enforce a Zero Trust model while maintaining user productivity.

---
