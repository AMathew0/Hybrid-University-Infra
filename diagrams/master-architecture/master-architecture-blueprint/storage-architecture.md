# 💾 **Storage Architecture Design (NAS, Cloud Sync, Backup)**

This step focuses on designing a **resilient, secure, and scalable storage strategy** using local NAS, cloud backup, RAID, and hybrid sync for both infrastructure and user-level data (students, staff, VMs).

---

## 🎯 Design Objectives

| Goal                   | Description                                                         |
| ---------------------- | ------------------------------------------------------------------- |
| Centralized Storage    | NAS for VM backups, ISO images, and shared staff data               |
| Departmental Access    | Segmented access for departments (via AD + NTFS permissions)        |
| Hybrid Sync            | Sync critical folders to AWS S3 / OneDrive for offsite backup       |
| Resilience & Recovery  | Use RAID + versioned backup to recover from ransomware or data loss |
| Cross-site Replication | Rsync between HQ and Bangalore for DR readiness                     |

---

## 🧱 Storage Components per Site

| Location       | NAS Device       | Specs                           | Purpose                        |
| -------------- | ---------------- | ------------------------------- | ------------------------------ |
| New Delhi (HQ) | Synology RS1221+ | 8-bay, RAID 6, 24 TB usable     | Primary backup, VM templates   |
| Bangalore      | QNAP TS-873A     | 8-bay, RAID 6, 24 TB usable     | Local file access, replication |
| Dubai          | Cloud only (S3)  | AWS S3 + Glacier (cold storage) | DR/long-term backups           |

---

## 📁 Folder Structure Example (HQ NAS)

```
/nas/
├── vm-backups/
│   ├── pve01/
│   └── pve02/
├── staff-data/
│   ├── admin/
│   ├── it/
│   └── finance/
├── shared/
│   └── manuals/
├── iso/
│   └── ubuntu.iso
└── archive/
    └── 2024-financials.zip
```

Permissions controlled via **Active Directory ACL** (Synology/QNAP integrated with Windows AD).

---

## 🔁 RAID & Resilience

| Type           | Location        | Purpose                          | Notes                         |
| -------------- | --------------- | -------------------------------- | ----------------------------- |
| RAID 6         | All on-prem NAS | Fault tolerance (2 disk failure) | Balance of speed + protection |
| Snapshot       | Enabled         | Prevent accidental deletion/mods | Hourly for shared folders     |
| AES Encryption | Enabled         | At-rest encryption               | Secure against data theft     |

---

## ☁️ Cloud Backup & Sync Strategy

| Tool        | Target              | Purpose                          | Notes                         |
| ----------- | ------------------- | -------------------------------- | ----------------------------- |
| rclone      | AWS S3 (B1 Glacier) | Weekly full sync from `/nas`     | Versioned, encrypted          |
| Synology C2 | Optional            | GUI cloud backup with retention  | Extra layer for critical docs |
| OneDrive    | Staff data only     | Automatically syncs `staff-data` | With M365 accounts            |
| rsync+SSH   | Bangalore <-> Delhi | Cross-site local backup          | Nightly cron job              |

---

## 🔧 Tools & Protocols Used

| Tool/Protocol  | Purpose                           | Notes                            |
| -------------- | --------------------------------- | -------------------------------- |
| SMB/CIFS       | Windows file sharing (staff)      | AD-joined clients map NAS shares |
| NFS            | For VM ISO/Backup mount (Proxmox) | Faster access on local network   |
| Rsync over SSH | Cross-site sync                   | Used for redundancy between NAS  |
| Rclone         | CLI sync to AWS S3 / OneDrive     | Used in automation scripts       |
| Snapshots      | File versioning on NAS            | For folders like `staff-data/`   |

---

## 🗓️ Backup Schedule (Example)

| What              | Location          | Frequency | Retention             |
| ----------------- | ----------------- | --------- | --------------------- |
| VM Backups        | NAS (HQ & B'lore) | Daily     | 7 Days                |
| Staff Folders     | NAS + OneDrive    | Real-time | SharePoint versioning |
| NAS → S3 Glacier  | AWS               | Weekly    | 12 Months             |
| rsync Replication | Between sites     | Nightly   | Last 3 versions       |

---

## 🧪 Sample Flow: Department File Access + Backup

```text
1. IT staff logs in using AD credentials (Laptop)
2. Mapped drive connects to \\nas\staff-data\it\
3. Staff edits documentation → Snapshots created every hour
4. rclone script syncs folder to AWS S3 (weekly)
5. In case of ransomware, restore from hourly NAS snapshot or S3
```

---

## 📦 Optional Features

| Feature            | Tool                 | Benefit                           |
| ------------------ | -------------------- | --------------------------------- |
| Antivirus for NAS  | Synology AV / ClamAV | Prevent malware propagation       |
| Cloud Archiving    | S3 + Glacier         | Low-cost storage for old data     |
| AD-Based Access    | Synology/QNAP        | Centralized permission management |
| 2FA for NAS Access | Synology             | Improved security for admin users |

---

## 🛠️ Suggested Enhancements

* **Enable HTTPS and firewall rules on NAS**
* Use **automated health check scripts** (`smartctl`, `mdadm`)
* Test **restore procedures monthly** (both from NAS and S3)
* Create **audit logs** for file access (for compliance)

---
