# 💾 Backup & Disaster Recovery Strategy – Hybrid University

This document outlines the Backup and Disaster Recovery (DR) framework implemented at Hybrid University to ensure data integrity, business continuity, and compliance with institutional IT governance.

---

## 🎯 Objectives

- Ensure **data availability** during failures or disasters.
- Enable **rapid recovery** with clearly defined RTO and RPO.
- Support **hybrid infrastructure** with both on-premises and cloud workloads.
- Automate and secure backup operations across environments.

---

## 🧰 Backup Components Overview

| Component           | Stack                                               | Description                                                                 |
|--------------------|-----------------------------------------------------|-----------------------------------------------------------------------------|
| VM/Server Backup    | Veeam Community Edition                             | Protects up to 10 VMs/workloads; snapshot-based VM backup                   |
| File-Level Backup   | Rsync + Rclone                                      | Local sync with Rsync; Cloud sync to AWS S3 via Rclone                      |
| Cloud-Native Backup | AWS Backup, Azure Backup                            | Backup EC2, RDS, S3 (AWS); M365 mailboxes, SharePoint (Azure)              |
| DR Site             | Proxmox (cold standby), AWS S3 offsite replication  | Proxmox standby node for recovery; critical data replicated to S3          |

---

## 🕒 Recovery Objectives

| Metric | Value                                |
|--------|--------------------------------------|
| **RTO** | 4 hours (for critical systems)       |
| **RPO** | 1 hour (for critical data sets)      |

---

## 🖥️ VM & Server Backup – Veeam

- **Tool**: [Veeam Backup & Replication Community Edition](https://www.veeam.com/virtual-machine-backup-solution-free.html)
- **Scope**: Up to 10 workloads (Proxmox, ESXi, Hyper-V, physical servers)
- **Frequency**: Daily incremental with weekly full
- **Storage**: Local NAS + offsite AWS S3 sync
- **Retention**: 30 days for full backups, 90 days for archives

---

## 📁 File-Level Backup – Rsync + Rclone

### Rsync:

- Used for incremental backups of critical directories (e.g., `/etc`, `/home`, `/var/lib`)
- Scheduled using `cron` for hourly or daily syncs

### Rclone:

- Syncs Rsync backups to **AWS S3 (Glacier or Standard)** for offsite redundancy
- Can also sync to **Backblaze B2**, **Azure Blob**, or **Google Cloud Storage**

```bash
# Example cron job
0 * * * * rsync -avz /home/ /mnt/backup/home/
0 2 * * * rclone sync /mnt/backup s3:hybrid-university-backups --progress

```

## ☁️ Cloud-Native Backups

AWS:
AWS Backup used for:

EC2 snapshots
RDS daily backups
S3 lifecycle rules for archiving

Azure:
Azure Backup for:
Microsoft 365: Exchange Online, SharePoint, OneDrive
Endpoint data (via Microsoft Defender integration)

## 🧯 Disaster Recovery Site – Cold Standby Node

Hardware: Secondary Proxmox node stored at a separate campus
Mode: Cold standby (powered down except for tests/monthly sync)
Backup Restoration: Veeam image restores or ISO-based deployment
Data Sync: Replicated via Rclone to offsite S3 bucket

## 📊 Monitoring & Reporting

Backup Logs: Collected from Veeam, Rsync, Rclone via syslog or Graylog
Health Reports: Weekly reports sent via email from Veeam and GLPI
Failure Alerts: Integrated with Zabbix for real-time alerting on backup failures

## 🔐 Security Measures

All backup data is encrypted using AES-256 before storage.
Rclone and AWS S3 use server-side encryption (SSE-S3/SSE-KMS).
Access to backup repositories is restricted via IAM roles and multi-factor authentication.

## ✅ Summary Table

Layer	Toolset Used	Backup Frequency	Retention	Offsite Sync
VM/Server	Veeam Community Edition	Daily/Weekly	30/90 Days	Yes (AWS S3 via Rclone)
File-Level	Rsync + Rclone	Hourly/Daily	7/30 Days	Yes
Cloud	AWS Backup / Azure Backup	Daily	As per policy	Built-in
DR Site	Cold Proxmox Node + S3 Replica	Weekly Snapshots	Manual Restore	Yes

## 📌 Conclusion
Hybrid University's Backup & Disaster Recovery strategy combines robust open-source tools with cloud-native services to ensure high availability, rapid recovery, and data integrity. With a defined RTO/RPO framework, the institution is prepared for operational disruptions while remaining cost-effective and compliant.

