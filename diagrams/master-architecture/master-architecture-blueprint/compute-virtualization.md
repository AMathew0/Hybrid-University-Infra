# 🖥️ Compute & Virtualization Architecture

---

## 🎯 Design Goals

| Objective              | Description                                               |
| ---------------------- | --------------------------------------------------------- |
| Virtualization         | Maximize server usage via Proxmox-based virtualization    |
| High Availability (HA) | Zero downtime for critical apps (LMS, ERP, AD, Git, etc.) |
| Disaster Recovery (DR) | Failover to alternate site or cold standby node           |
| Central Management     | Web-based management, snapshotting, backups               |
| Hybrid Workloads       | Mix of on-prem (Proxmox) + cloud (AWS EC2, RDS, S3)       |

---

## 🧱 Infrastructure Stack Per Site

| Component            | HQ (New Delhi)              | Campus (Bangalore)    | Dubai Teaching Site      |
| -------------------- | --------------------------- | --------------------- | ------------------------ |
| Proxmox Nodes        | 2 Active + 1 Cold DR        | 1 Node                | None (Cloud access only) |
| NAS (RAID 6)         | 1 Primary                   | 1 for local backups   | S3 / OneDrive            |
| L3 Switch + Firewall | HA Pair (Cisco + FortiGate) | FortiGate + L2 Switch | FortiClient + VPN only   |

---

## 🔁 Proxmox VE Cluster Design

| Node  | Role              | CPU (Example)     | RAM    | Storage   | Use Case                     |
| ----- | ----------------- | ----------------- | ------ | --------- | ---------------------------- |
| pve01 | Primary Node      | Intel Xeon Silver | 128 GB | 2 TB NVMe | Core infra (AD, Git, Zabbix) |
| pve02 | Secondary Node    | Intel Xeon Silver | 128 GB | 2 TB SSD  | App servers (LMS, ERP, etc.) |
| pve03 | DR (Cold Standby) | Intel Xeon Bronze | 64 GB  | 1 TB SSD  | Manual failover (Site 2)     |

All nodes are connected via **dedicated 10Gbps backend LAN for cluster sync & Ceph/GlusterFS (optional)**.

---

## 🗂️ VM Roles & Allocation (Sample)

| VM Name       | Resources      | Host Node | Function                            |
| ------------- | -------------- | --------- | ----------------------------------- |
| ad-dc01       | 4 vCPU / 8 GB  | pve01     | Active Directory Domain Controller  |
| moodle-lms    | 6 vCPU / 12 GB | pve02     | Moodle LMS (Apache + PHP + MariaDB) |
| git-server    | 2 vCPU / 4 GB  | pve01     | Gitea (or GitHub Actions runner)    |
| zabbix-server | 4 vCPU / 8 GB  | pve01     | Monitoring (Zabbix + frontend)      |
| bookstack     | 2 vCPU / 2 GB  | pve02     | Internal Wiki (BookStack)           |
| glpi-helpdesk | 2 vCPU / 4 GB  | pve02     | Helpdesk / ITSM system              |

---

## 🔧 Proxmox Key Features Enabled

| Feature         | Purpose                                                    |
| --------------- | ---------------------------------------------------------- |
| Live Migration  | Move VMs between nodes without downtime                    |
| Snapshots       | Restore points before updates/config changes               |
| Backup Schedule | Nightly backups to NAS (via Proxmox backup server)         |
| Templates       | For standardized OS deployments (Ubuntu 24.04, Windows 11) |
| Tags & Groups   | Organize VMs by type, department                           |

---

## 💾 Storage Strategy

| Storage Type    | Location          | Use Case                      |
| --------------- | ----------------- | ----------------------------- |
| Local SSD/NVMe  | Each Proxmox node | Fast access for active VMs    |
| NAS (RAID 6)    | HQ & Remote       | VM backups, ISO storage       |
| AWS S3 (Cold)   | Cloud             | Monthly snapshots, DR backups |
| Ceph (optional) | Cluster shared    | HA + live migration           |

> Daily backup to NAS; weekly full backup to **AWS S3 Glacier** for DR compliance.

---

## 🧯 High Availability & Disaster Recovery

| Feature      | Method                                       |
| ------------ | -------------------------------------------- |
| HA Cluster   | Proxmox HA Manager + shared storage          |
| DR Strategy  | Cold standby in Bangalore or cloud VM import |
| Failover DNS | Cloudflare DNS failover (manual)             |
| Replication  | ZFS send or Proxmox built-in replication     |

---

## ☁️ Cloud-Integrated Compute Use (Hybrid)

| App                | Cloud Component               | Notes                      |
| ------------------ | ----------------------------- | -------------------------- |
| Moodle DB          | AWS RDS PostgreSQL (optional) | Offload DB load            |
| GitHub CI          | GitHub Actions                | CI/CD pipelines            |
| Zoom               | EDU cloud account             | No infra load              |
| SharePoint / M365  | Microsoft Cloud               | No infra required          |
| AWS EC2 (optional) | DR VMs during failover        | Manual boot from snapshots |

---

## 📶 Sample Flow: Student Accessing LMS

```text
1. Student logs into Moodle via public DNS (LMS.univ.edu)
2. Cloudflare forwards to FortiGate → DNAT to moodle-lms VM
3. Moodle authenticates via Azure AD (SAML)
4. Moodle DB hosted on local MariaDB or AWS RDS (depending on scale)
5. Moodle writes logs to central Graylog + Zabbix monitors performance
```

---

## 📦 Sample Backup Schedule

| Target       | Frequency  | Method          | Retention        |
| ------------ | ---------- | --------------- | ---------------- |
| Local NAS    | Daily      | Proxmox Backup  | 7 days           |
| S3 Glacier   | Weekly     | rclone / script | 1 year           |
| VM Snapshots | Pre-change | Proxmox native  | 3-5 snapshot max |

---
