# 🔐 Storage Architecture & Backup Strategy

### 🎯 Objective

Design a robust, scalable, and fault-tolerant storage and backup solution to protect academic, administrative, and research data across all university sites (New Delhi, Bangalore, Dubai) with a mix of on-prem and cloud storage.

---

## 🧱 1. Storage Architecture Overview

| Type               | Purpose                               | Technology Stack                              |
| ------------------ | ------------------------------------- | --------------------------------------------- |
| On-Prem NAS        | Departmental shared drives            | Synology / TrueNAS with RAID-6                |
| File Collaboration | Personal/staff/student files          | SharePoint Online, OneDrive for Business      |
| Research Data      | Large files, raw data, structured DBs | AWS S3 Glacier, Azure Blob Archive, local NAS |
| LMS Content        | Moodle backups, assets                | EC2 + EBS (with lifecycle rules)              |
| SIS & ERP          | DB + transactional backups            | AWS RDS + RDS snapshot backup                 |
| VM/Infra Backup    | Complete system backup                | Veeam CE / Proxmox backup server              |

---

## 🗄️ 2. Logical Layout (Per Site)

```plaintext
Each site: 
  - NAS for local storage (RAID-6, hot-swappable)
  - Dedicated backup NAS (1:1 rsync, isolated VLAN)
  - Internet access for S3 sync and offsite DR
```

---

## 🌐 3. Backup Strategy

| Data Type           | Tool/Method                            | Frequency    | Retention         |
| ------------------- | -------------------------------------- | ------------ | ----------------- |
| User Files (NAS)    | Rsync + Rclone to AWS S3               | Daily        | 30d / Archive 1yr |
| Cloud Drives        | Microsoft 365 Retention + Azure Backup | Continuous   | Default + Archive |
| VMs (Proxmox)       | Veeam CE or native Proxmox backup      | Daily        | 7 days + monthly  |
| RDS / Databases     | AWS RDS Snapshots                      | Hourly/Daily | 7 days            |
| Moodle File Backup  | Local tarball + S3 Glacier archive     | Weekly       | 6 months          |
| Configuration Files | GitHub Private Repo / Encrypted S3     | On-change    | 1yr               |

---

## 📊 4. RTO/RPO Matrix

| Critical System  | RTO      | RPO     | Site          |
| ---------------- | -------- | ------- | ------------- |
| ERP/SIS          | 4 hours  | 1 hour  | Cloud-Hosted  |
| Moodle LMS       | 6 hours  | 2 hours | Hybrid (EC2)  |
| File Share (NAS) | 8 hours  | 4 hours | All Locations |
| User Devices     | 12 hours | Daily   | Local Backups |

---

## 🛠️ 5. Disaster Recovery Approach

| Site      | DR Strategy                    | Tools Used                  |
| --------- | ------------------------------ | --------------------------- |
| New Delhi | Primary + cold standby         | Proxmox + Rsync + S3        |
| Bangalore | Secondary site (active-active) | FortiGate VPN + NAS replica |
| Dubai     | Tertiary site (read-only)      | Backup NAS + Cloud restore  |

---

## 🧪 6. Example Flow: Daily File Backup

```plaintext
1. Files created on Dept NAS (RAID-6)
2. Nightly Rsync → Backup NAS
3. Rclone → AWS S3 (encrypted)
4. Lifecycle → Glacier after 30 days
5. Monthly check of integrity logs
```

---

## 🔒 7. Security & Compliance

* ✅ AES-256 encryption at rest (NAS, S3)
* ✅ TLS 1.2 for Rsync/SSH
* ✅ MFA on backup consoles
* ✅ Immutable backup versions (S3 + Veeam)
* ✅ GDPR / Data Residency compliance (based on site)

---

## 📝 Storage Policy Governance

| Policy Type      | Description                          |
| ---------------- | ------------------------------------ |
| Versioning       | Enabled on S3 and OneDrive           |
| Lifecycle Policy | Auto-archive data older than 30 days |
| Access Control   | Role-based (via Azure AD / NAS ACLs) |
| Audit Logs       | Enabled on all NAS + backup software |

---
