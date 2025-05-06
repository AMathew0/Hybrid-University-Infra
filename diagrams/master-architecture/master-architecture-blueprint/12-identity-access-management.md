# 🔐 **Identity & Access Management (IAM) Design**

🎯 **Goal**: Establish a **secure, centralized, and scalable IAM system** for multi-site, hybrid university infrastructure — covering both cloud and on-prem environments.

---

## 🧱 Architecture Overview

| IAM Component         | Tool/Service                | Purpose                                            |
| --------------------- | --------------------------- | -------------------------------------------------- |
| Primary Directory     | **Windows Server AD**       | Central identity store (hybrid synced to Azure AD) |
| Cloud Directory       | **Azure Active Directory**  | Federated identity for SaaS and SSO apps           |
| Federation (Optional) | **Google Workspace**        | Optional auth for students (OAuth2, SAML)          |
| MFA / 2FA             | **Microsoft Authenticator** | Secures access to all critical systems             |
| SSO                   | **Azure AD SSO**            | Unified login for Moodle, GitHub, Zoom, etc.       |
| Provisioning          | **SCIM / PowerShell**       | Automate account lifecycle                         |

---

## 🔄 Identity Federation Flow

```text
1. Users are created in AD (faculty/staff) or Google (students)
2. Azure AD Connect syncs AD → Azure AD (every 30 mins)
3. Azure AD provides SSO & MFA for all cloud apps
4. Google Workspace federates student login via SAML to LMS, GitHub
5. Deprovisioning handled via AD & Google Groups policies
```

---

## 👥 User Directory Model

| User Type     | Source Directory | Login Method         | Provisioned Apps                       |
| ------------- | ---------------- | -------------------- | -------------------------------------- |
| Faculty/Staff | AD + Azure AD    | AD → Azure SSO + MFA | M365, SharePoint, Moodle, Zoom, GitHub |
| Students      | Google Workspace | SAML / OAuth2        | Moodle, Gitea/GitHub EDU, OneDrive     |
| IT/Admin      | AD (Privileged)  | MFA required         | Admin portals (AWS, Proxmox, Zabbix)   |

---

## 🏢 Multi-Site IAM Topology

```text
Site 1 (New Delhi):
  - Windows AD DC (primary)
  - Azure AD Connect

Site 2 (Bangalore):
  - Windows AD DC (secondary + GC)

Site 3 (Dubai):
  - Lightweight AD Replica
  - Azure AD cloud fallback

All sites:
  - Azure AD SSO
  - Google Workspace logins (SAML for Moodle)
```

---

## 🔐 MFA (Multi-Factor Auth) Strategy

| Role     | Method                  | Enforced on                          |
| -------- | ----------------------- | ------------------------------------ |
| Admins   | Microsoft Authenticator | All logins (local/cloud)             |
| Faculty  | Email + Authenticator   | Cloud apps + VPN                     |
| Students | Optional (future)       | Web SSO (optional rollout in phases) |

---

## 🔑 Role-Based Access Control (RBAC)

| Group               | Access Scope                                  |
| ------------------- | --------------------------------------------- |
| `IT-Admins`         | Full infra, VMs, GitHub, AWS, firewall        |
| `Faculty-Sec-Staff` | Moodle (teach), SharePoint (RW), Email (Full) |
| `Students-UG`       | Moodle (student), OneDrive, GitHub EDU        |
| `Research-GRP`      | NAS + Proxmox VM access + Moodle + GitHub     |
| `Guest-Lecturers`   | Temporary access via Azure AD group           |

RBAC integrated across AD groups → Azure AD → Apps.

---

## 🔁 Account Lifecycle Automation

| Stage        | Method                             | Tool/Script              |
| ------------ | ---------------------------------- | ------------------------ |
| Provisioning | AD/Google form → PowerShell script | Azure AD + CSV + PS      |
| Updates      | Group-based via AD/Azure AD        | Auto-sync                |
| Deletion     | Scripted offboarding workflow      | Locks + Archive + Notify |

---

## 🧪 Example Flow: Faculty Login to Moodle

```text
1. Faculty logs into Moodle
2. Moodle redirects to Azure AD SSO
3. Azure AD validates credentials + MFA
4. User attributes sent via SAML
5. Moodle grants access based on AD group
```

---

## 🔐 Security Best Practices

* 🔒 **Enable Conditional Access Policies** in Azure AD
* 📅 **Rotate privileged accounts' credentials every 90 days**
* 🛡️ **Limit admin accounts from Internet access (VPN only)**
* 💡 **Set session timeout policies for web SSO**
* 📝 **Log all authentication attempts via Azure AD + Graylog**

---

## 📦 Tools Summary

| Tool/Service            | Role                     | Notes                        |
| ----------------------- | ------------------------ | ---------------------------- |
| Windows AD              | Local identity store     | Redundant domain controllers |
| Azure AD                | Cloud identity + SSO     | Federation + MFA             |
| Google Workspace        | Student identity         | SAML/OAuth2 apps             |
| Microsoft Authenticator | MFA                      | Push notifications or OTP    |
| PowerShell/SCIM         | Provisioning & lifecycle | Manual or automated          |

---

## ✅ Compliance Alignment

| Control Area    | IAM Solution                            |
| --------------- | --------------------------------------- |
| Access Control  | RBAC + Group Policies                   |
| User Management | Automated provisioning & deprovisioning |
| Audit & Review  | Logs via Azure + Graylog                |
| MFA Enforcement | Mandatory for admins + faculty          |

---
