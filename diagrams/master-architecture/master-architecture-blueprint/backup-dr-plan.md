# 🔁 **Backup & Disaster Recovery (DR) Plan**

This step outlines a **multi-layered backup and disaster recovery strategy** for the hybrid university infrastructure. The objective is to ensure business continuity, data protection, and rapid recovery in the event of data loss, ransomware, site failure, or human error.

---

## 🎯 Objectives

| Objective                | Description                                                      |
| ------------------------ | ---------------------------------------------------------------- |
| Protect critical systems | LMS, AD, NAS, SIS, Git, ERP, Email                               |
| Ensure quick recovery    | Minimal downtime (RTO) and data loss (RPO) for critical services |
| Multi-site DR strategy   | Leverage second site + cloud for DR                              |
| Comply with retention    | Weekly/monthly/yearly retention plans for audits and compliance  |

---

## 🧱 Backup Targets

| Component            | Type          | Location       | Backup Tool              | Frequency |
| -------------------- | ------------- | -------------- | ------------------------ | --------- |
| Virtual Machines     | Full image    | NAS + S3       | Proxmox + Veeam CE       | Daily     |
| NAS Shared Folders   | File-level    | AWS S3 Glacier | rclone / Synology        | Weekly    |
| M365 Mail/Drive      | Cloud-native  | Azure Backup   | Veeam Backup for M365\*  | Daily     |
| Git Repos (Gitea)    | Full mirror   | NAS + S3       | Scripted `git clone`     | Daily     |
| AD/DC Config         | Config export | NAS            | PowerShell backup script | Weekly    |
| Databases (Postgres) | SQL dump      | NAS + S3       | `pg_dump` + cron         | Daily     |
| Moodle Files         | App + DB      | NAS + S3       | Rsync + pg\_dump         | Daily     |

\*Open source alternatives: `rclone` + Azure CLI + Python scripts

---

## 🔐 Backup Retention Policy

| Type          | Retention Duration | Storage Medium       |
| ------------- | ------------------ | -------------------- |
| Daily Backups | 7 Days             | NAS (RAID 6)         |
| Weekly        | 4 Weeks            | S3 Standard          |
| Monthly       | 6 Months           | S3 Glacier           |
| Yearly        | 7 Years (Audit)    | Glacier Deep Archive |

---

## ⚠️ Critical RTO/RPO Targets

| Service             | RTO       | RPO        | Notes                                      |
| ------------------- | --------- | ---------- | ------------------------------------------ |
| Active Directory    | 2 hours   | 30 minutes | Core auth — needed for all user access     |
| LMS (Moodle)        | 4 hours   | 1 hour     | Must restore DB and storage together       |
| NAS (shared data)   | 6 hours   | 1 hour     | Staff and department documents             |
| Email (M365)        | 2 hours   | 15 minutes | Microsoft SLA included                     |
| Student Info System | 2–4 hours | 1 hour     | Postgres DB dump + App container           |
| Proxmox VM Host     | 4 hours   | 1 hour     | Restore to standby node from image backups |

---

## 🔁 DR Site Strategy

| Site      | DR Type         | Notes                                           |
| --------- | --------------- | ----------------------------------------------- |
| Bangalore | Warm Site       | Sync VM backups, clone infra, scheduled testing |
| Dubai     | Cold + Cloud DR | Used for last-resort failover (S3 restore)      |

**Test Plan**: Simulated quarterly failover from Delhi → Bangalore

---

## 🛠️ Tools & Technologies

| Tool                | Purpose                        | Notes                       |
| ------------------- | ------------------------------ | --------------------------- |
| Veeam CE            | Full VM & File backup          | Free up to 10 workloads     |
| Rsync               | File-level sync to other sites | Secure, simple, scriptable  |
| Rclone              | Cloud backup (S3, OneDrive)    | Lightweight and automated   |
| pg\_dump            | PostgreSQL backup              | Used for LMS, ERP, SIS      |
| Azure Backup        | M365 mail, Teams, OneDrive     | Optional, GUI-based         |
| GitLab/Gitea Script | Repo mirroring                 | `git clone --mirror` weekly |

---

## 🔄 Sample Backup Flow: Moodle LMS (Self-hosted)

```text
1. pg_dump exports the database nightly to `/backups/lms-db/`
2. Rsync syncs Moodle content directory to `/backups/lms-files/`
3. rclone syncs both folders to AWS S3 (versioned)
4. Veeam backs up EC2 VM image to NAS
5. If VM crashes, restore from Proxmox backup, then re-import DB
```

---

## 🧪 Example DR Drill (Quarterly)

```text
Scenario: New Delhi Datacenter Power Failure
1. Notify IT team via monitoring alert (Zabbix)
2. Spin up standby Proxmox host in Bangalore
3. Restore VM images from local NAS (or AWS S3)
4. Validate AD, Moodle, and SIS access
5. Failback to Delhi after issue resolved
```

---

## 🧰 Suggested Enhancements

* 🔐 **Enable at-rest and in-transit encryption** (AES-256 + SSL)
* 🔁 **Automate backup scripts using systemd timers or Task Scheduler**
* 🔁 **Version all backups in cloud (for ransomware rollback)**
* ✅ **Monthly restore testing from each storage level (NAS, S3, Glacier)**
* 🔍 **Use monitoring (Zabbix) to alert on failed backups**

---

## 🔐 Compliance & Audit

| Type             | Strategy                                             |
| ---------------- | ---------------------------------------------------- |
| Audit Logs       | Store logs of each backup run + file integrity check |
| Immutable Backup | Use S3 Object Lock / Glacier Vault Lock              |
| GDPR/FERPA       | Encrypt personal data; store in EU/India             |

---
