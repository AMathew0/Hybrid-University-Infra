### 📚 Documentation & ITSM

This section outlines the internal documentation, knowledge base, IT support, and policy management setup for a hybrid IT environment supporting both cloud-native and on-premises systems.

---

## 🛠️ Purpose & Tools

| Purpose            | Tool/Technology                            |
| ------------------ | ------------------------------------------ |
| Documentation / KB | MkDocs (Material for MkDocs theme)         |
| Diagramming        | Draw\.io / Diagrams.net                    |
| Ticketing / ITSM   | GLPI (self-hosted) or Freshservice (cloud) |
| IT Policy Storage  | GitHub repo (Markdown + PDF export)        |

---

## 🔧 MkDocs Setup

**Location:**
`/12-documentation-itsm/docs/`

**Features:**

* Clean developer-style KBs
* Material theme for navigation/search
* Static site hosted via GitHub Pages or S3 + CloudFront
* Versioned documentation per domain/project

**Basic Structure Example:**

```
docs/
├── index.md
├── network/
│   └── vlan-setup.md
├── apps/
│   └── moodle-configuration.md
└── policies/
    └── password-policy.md
```

**mkdocs.yml:**

```yaml
site_name: Hybrid University IT Docs
theme:
  name: material
nav:
  - Home: index.md
  - Network: 
    - VLAN Setup: network/vlan-setup.md
  - Applications:
    - Moodle Config: apps/moodle-configuration.md
  - Policies:
    - Password Policy: policies/password-policy.md
```

---

## 🖼️ Diagrams

* Source files stored under:
  `12-documentation-itsm/diagrams/`

* Editable in: [https://app.diagrams.net](https://app.diagrams.net)

**Examples:**

* Network Topology
* DR Workflow
* VM/Cluster Layout
* ITSM Flowchart

---

## 🎫 Ticketing / ITSM

| Solution     | Type        | Use Case                         |
| ------------ | ----------- | -------------------------------- |
| GLPI         | Self-hosted | Internal IT Helpdesk + Assets    |
| Freshservice | Cloud-based | Lightweight ITSM + Email Gateway |

* Auto-assign via user groups (HR, Dev, IT)
* Email-to-ticket integration: `support@yourdomain.edu`
* LDAP / Azure AD integration for SSO (optional)
* Asset tracking via GLPI plugins or barcode scan

---

## 🧾 IT Policies

| Policy Type       | Format       | Notes                                |
| ----------------- | ------------ | ------------------------------------ |
| Acceptable Use    | Markdown/PDF | Reviewed annually                    |
| Password Policy   | Markdown/PDF | MFA, expiry, complexity              |
| BYOD Policy       | Markdown/PDF | Device registration & network access |
| Incident Response | Markdown/PDF | Steps, contacts, escalation flow     |

* Version-controlled in Git (`/policies/`)
* Exports in PDF using `pandoc` or MkDocs PDF plugin

---

## ✅ Summary

This module ensures:

* 📖 Easily accessible internal knowledge base
* 🧰 Clear IT workflows and ticket lifecycle
* 🔒 Transparent IT policies and compliance
* 📊 Visual clarity with network/system diagrams

---
