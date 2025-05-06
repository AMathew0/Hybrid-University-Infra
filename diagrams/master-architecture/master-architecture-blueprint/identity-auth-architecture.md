
# 🔐 Step 6: Identity & Authentication Architecture 

This is a **core component** of the system, ensuring seamless login, secure access control, and unified identity management for students, faculty, staff, and IT teams across **on-prem** and **cloud systems**.

---

## 🧭 Identity Goals

| Objective                        | Description                                                  |
| -------------------------------- | ------------------------------------------------------------ |
| Unified Identity                 | One identity per user across all systems                     |
| Single Sign-On (SSO)             | One login to access all tools (LMS, ERP, GitHub, Zoom, etc.) |
| Multi-Factor Authentication      | Mandatory 2FA for admin/staff, optional for students         |
| Role-Based Access Control (RBAC) | Department-based permissions across systems                  |
| Federation Support               | Google Workspace support for student logins (optional)       |
| Lifecycle Management             | Auto-provisioning/deprovisioning from HRIS/SIS               |

---

## 🗂️ Identity Directory Components

| Component         | Tool / Service                   | Role                                                    |
| ----------------- | -------------------------------- | ------------------------------------------------------- |
| Primary Directory | Windows Active Directory (AD DS) | Central identity store, synced to Azure AD              |
| Cloud Directory   | Azure Active Directory           | Unified SSO & conditional access for all cloud services |
| Federation IDP    | Google Workspace (Students)      | OAuth2 / SAML federation for student BYOD devices       |
| Password Mgmt     | Azure AD + Self-Service Reset    | Users can reset passwords securely                      |

---

## 🌐 Authentication Protocols Used

| Protocol        | Purpose                        | Notes                           |
| --------------- | ------------------------------ | ------------------------------- |
| Kerberos        | On-prem Windows Auth           | Used for domain-joined systems  |
| LDAP(S)         | Legacy apps / integrations     | Used for internal-only services |
| SAML 2.0        | Federation (LMS, Zoom, GitHub) | Enables SSO                     |
| OAuth2 / OIDC   | Modern web app auth            | Used for custom apps, GitHub    |
| RADIUS / WPA2-E | Wi-Fi auth via AD              | Students/Staff authenticated    |

---

## 🔐 MFA Strategy

| Group           | Method                         | Tool                          |
| --------------- | ------------------------------ | ----------------------------- |
| Staff & Faculty | Mandatory                      | Microsoft Authenticator       |
| Students        | Optional (encouraged)          | Google / Microsoft Auth App   |
| Admins          | Mandatory + Conditional Access | Microsoft Auth + IP filtering |

---

## 🧩 Integration Overview

| System             | Authentication Type      | Federated ID Source           |
| ------------------ | ------------------------ | ----------------------------- |
| Moodle LMS         | SAML (Azure AD + Google) | AD (staff), Google (students) |
| GitHub Org         | SAML SSO (Azure AD)      | AD accounts only              |
| Zoom (Edu)         | SSO (SAML via Azure AD)  | Same as AD + MFA enforced     |
| ERP/SIS (Custom)   | OAuth2 / OIDC            | Azure AD                      |
| Wi-Fi (802.1x)     | RADIUS via NPS + AD      | Username/password (AD)        |
| Linux Admin Access | SSH + LDAP               | Restricted to admin group     |
| Windows Devices    | AD Join + Intune MDM     | Domain-joined w/ policy       |

---

## 🧪 Example: Staff Login Flow

```text
1. Staff member opens browser and accesses Moodle.
2. Moodle redirects to Azure AD SSO.
3. Azure AD checks group (staff) and forces MFA.
4. User authenticates via Microsoft Authenticator app.
5. Azure grants SAML token and redirects back to Moodle with session.
```

---

## 🧪 Example: Student Login Flow via Google

```text
1. Student accesses LMS or Zoom from personal device.
2. Redirected to choose Google SSO.
3. Google authenticates email (student@univ.edu).
4. Upon first login, LMS creates linked profile (just-in-time provisioning).
```

---

## 🛠️ Tools & Services Breakdown

| Layer         | Tool Used                         | Notes                                    |
| ------------- | --------------------------------- | ---------------------------------------- |
| Identity Sync | Azure AD Connect                  | Syncs on-prem AD with Azure              |
| Federation    | Azure AD Enterprise Apps + SAML   | SAML integration for third-party tools   |
| Self-Service  | Microsoft SSPR                    | Password reset for all users             |
| MFA & Policy  | Conditional Access + MFA in Azure | Enforce policies based on group/location |
| Guest Access  | Azure AD B2B                      | Invite-based external accounts           |

---

## 🔁 Identity Lifecycle Flow

```text
1. HR or SIS adds new user (staff or student).
2. Entry triggers user creation in AD / Google Workspace.
3. Azure AD syncs updated accounts every 30 mins.
4. Account auto-provisioned in LMS, email, and GitHub.
5. On graduation/resignation, account disabled from central AD.
6. Auto-deprovisioned from services (via SCIM or manual cleanup).
```

---

## ✅ Identity Architecture Summary

* [x] Windows AD as Primary Source of Truth
* [x] Azure AD for SSO, MFA, Federation
* [x] Google Workspace integration (optional)
* [x] MFA for all critical accounts
* [x] Clean identity lifecycle and RBAC per department

---
