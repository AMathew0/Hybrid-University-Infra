## **Directory Services Architecture**
---

### **Overview**

This architecture focuses on centralized identity and access management across multiple campuses (New Delhi, Bangalore, Dubai) and cloud environments (Azure, Google, AWS). It includes:

* **Hybrid Active Directory (AD)** with Azure AD Sync
* **Role-based access controls** via OUs and security groups
* **Federation** with Google Workspace (for students)
* **SSO for all applications** (Moodle, ERP, GitHub, etc.)

---

## **1. Core Identity Strategy**

| Component                              | Role                                                           |
| -------------------------------------- | -------------------------------------------------------------- |
| **Windows Active Directory (on-prem)** | Central identity system for all staff & faculty                |
| **Azure AD (cloud)**                   | Federated cloud identity for hybrid login, MFA, SSO            |
| **Azure AD Connect**                   | Sync on-prem AD to Azure AD                                    |
| **Google Workspace**                   | Optional SAML Federation for student accounts                  |
| **LDAP Proxy (optional)**              | For apps that require LDAP and live in cloud or isolated zones |

---

## **2. OU (Organizational Unit) Design**

```
HybridUniversity.local
│
├── Staff
│   ├── Faculty
│   ├── Admin
│   ├── IT
│
├── Students
│   ├── UG
│   ├── PG
│   └── Alumni
│
├── Devices
│   ├── Laptops
│   ├── Labs
│   └── Kiosks
│
├── Servers
│   ├── CoreInfra
│   ├── Application
│   └── Backup
```

* **GPOs** apply per OU (e.g., security hardening for laptops, login scripts for students)
* **Naming Convention:** `site-dept-userid` (e.g., `DEL-IT-john.doe`)

---

## **3. Sites & Services in AD**

Use **Active Directory Sites and Services** to map:

| Site          | DC Count | Role                               |
| ------------- | -------- | ---------------------------------- |
| New Delhi     | 2        | Primary AD, FSMO roles, GPO mgmt   |
| Bangalore     | 1        | Read-Write DC                      |
| Dubai         | 1        | Read-only DC (RODC) for latency    |
| Azure (Cloud) | 1        | Azure AD Connect + ADFS (optional) |

**Replication:** Use site links to optimize AD replication.
**DNS Integration:** Each site runs DNS integrated with AD.

---

## **4. Azure AD Sync**

| Feature       | Details                              |
| ------------- | ------------------------------------ |
| **Sync Tool** | Azure AD Connect (lightweight agent) |
| **Sync Type** | Password hash sync + device sync     |
| **MFA**       | Enabled via Azure Conditional Access |
| **SSO Apps**  | Moodle, ERP, Zoom, GitHub, Bookstack |

---

## **5. Google Workspace Integration (Optional)**

| Purpose     | Tool/Method                  |
| ----------- | ---------------------------- |
| Student SSO | Google SAML → Azure AD proxy |
| Email/Docs  | Google Workspace EDU         |
| Group Sync  | GAM Scripts (optional)       |

---

## **6. IAM for Cloud Environments**

| Cloud | Directory Integration               |
| ----- | ----------------------------------- |
| Azure | Native Azure AD (RBAC for services) |
| AWS   | AWS IAM, federated via SAML         |
| GCP   | Google Identity / SAML              |

**Best Practice:** Use Azure AD as IdP and federate to AWS/GCP using SAML or OIDC.

---

## **7. Group Management**

| Type                   | Examples                                      |
| ---------------------- | --------------------------------------------- |
| **Security Groups**    | `SG-Faculty-DEL`, `SG-IT-Admin`               |
| **Distribution Lists** | `DL-Students-PG`, `DL-Admin-Delhi`            |
| **Dynamic Groups**     | Based on attributes (e.g., `Department = IT`) |

---

## **8. Authentication & Federation**

| Feature      | Tool                                      |
| ------------ | ----------------------------------------- |
| Primary Auth | Kerberos / NTLM (on-prem), OAuth2 (cloud) |
| MFA          | Microsoft Authenticator / FIDO2 Keys      |
| SSO          | Azure AD (SAML/OAuth2)                    |
| Federation   | Google Workspace (SAML for students)      |

---

## **9. Diagram Suggestion (for later)**

* **Core Identity Diagram** showing:

  * AD DCs per site
  * Azure AD Connect
  * Federation flows to Google / AWS / GCP
  * MFA/SSO logic
---
