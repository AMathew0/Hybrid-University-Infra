# 📦 Core Infrastructure & Server Architecture

This step focuses on the **critical systems**, their **placement across sites**, and how they ensure **availability**, **scalability**, and **security** across your **multi-site hybrid university** setup.

---

## 🧱 Core Objectives

* Support services like LMS, SIS, Email, Intranet, Git, ERP, and video conferencing.
* Mix of **on-prem** (New Delhi, Bangalore) and **cloud** (Azure, AWS, M365) for flexibility.
* High Availability (HA) and Disaster Recovery (DR) built-in.
* Containerized app stack for dev and modular deployment.

---

## 📍 Site Role Allocation (Logical)

| Site      | Role                        | Purpose                                              |
| --------- | --------------------------- | ---------------------------------------------------- |
| New Delhi | **Primary Site**            | AD, File Server, Proxmox Cluster, Primary DB, LMS    |
| Bangalore | **Secondary/DR Site**       | Cold standby Proxmox node, Backup DB, File Sync      |
| Dubai     | **Cloud-only Edge**         | For local access, VPN gateway, edge proxies          |
| AWS       | Cloud-hosted LMS, S3 backup | Moodle, File backups, RDS replica                    |
| Azure     | Azure AD, Defender, M365    | Auth, Security, Exchange Online, OneDrive/SharePoint |

---

## 🏗️ Core Infrastructure Stack

| Layer              | Tech Stack / Tools                     | Location / Notes                                   |
| ------------------ | -------------------------------------- | -------------------------------------------------- |
| **Virtualization** | Proxmox VE 8.x                         | On-prem New Delhi (2 nodes), Bangalore (1 standby) |
| **Directory**      | Windows Server 2022 AD + Azure AD Sync | 1 DC on-prem + Azure AD cloud sync                 |
| **Email**          | Microsoft Exchange Online              | Via M365 cloud                                     |
| **LMS**            | Moodle 4.3 (Dockerized)                | Hosted in AWS EC2, DNS via Route 53                |
| **Database**       | PostgreSQL 15 + MariaDB                | AWS RDS + local HAProxy (for failover)             |
| **ERP / SIS**      | Custom Web (Django) + PostgreSQL       | On-prem + Cloud-ready containers                   |
| **Intranet**       | BookStack + Gitea                      | Dockerized, hosted in Delhi                        |
| **File Storage**   | TrueNAS Core (RAID 6) + Rsync          | On-prem, Rsync to Bangalore + AWS S3               |
| **Monitoring**     | Zabbix + Grafana                       | Central in Delhi, agents at all sites              |
| **Logging**        | Graylog                                | Logs from FortiGate, AD, Proxmox, apps             |
| **Firewall**       | FortiGate 100D (x2)                    | Per site, HA pair in Delhi, standalone elsewhere   |

---

## ⚙️ Core Server Setup – New Delhi (Primary Site)

| Server Name | Purpose                      | Host Type        | OS / App Stack                |
| ----------- | ---------------------------- | ---------------- | ----------------------------- |
| DC01        | Primary AD Domain Controller | VM               | Windows Server 2022           |
| DC02        | Secondary AD + DNS           | VM               | Windows Server 2022           |
| PROX-APP01  | Moodle, SIS, ERP, BookStack  | VM / Docker Host | Ubuntu 24.04 + Docker Compose |
| DB-PRIMARY  | PostgreSQL, MariaDB          | VM               | Ubuntu 24.04                  |
| FILE01      | File Server (RAID + Rsync)   | Bare Metal       | TrueNAS Core                  |
| ZBX01       | Zabbix Server + Grafana      | VM               | Ubuntu 24.04                  |
| GL01        | Graylog + MongoDB            | VM               | Ubuntu 24.04                  |
| GITEA01     | Self-hosted Git              | VM / Docker      | Alpine / Ubuntu               |

---

## 🛰️ DR/Secondary Site – Bangalore

| Server Name | Purpose                    | Host Type    | Notes              |
| ----------- | -------------------------- | ------------ | ------------------ |
| PROX-DR01   | Proxmox Cold Standby       | Bare Metal   | Failover ready     |
| DB-REPLICA  | PostgreSQL + Rsync Replica | VM           | Hot standby        |
| FILE-BKP01  | Rsync Target NAS           | TrueNAS Core | Replication target |

---

## 🌩️ Cloud Components

| Service        | Platform | Purpose                     | Notes                                |
| -------------- | -------- | --------------------------- | ------------------------------------ |
| Moodle EC2     | AWS      | Scalable LMS Deployment     | Docker stack                         |
| PostgreSQL RDS | AWS      | Primary LMS DB              | Automated backup & snapshot          |
| Azure AD       | Azure    | Identity + MFA              | Sync with on-prem AD                 |
| Microsoft 365  | Azure    | Email, OneDrive, SharePoint | Used by all staff/students           |
| AWS S3         | AWS      | Backup Storage              | Encrypted backups via lifecycle mgmt |

---

## 🔁 High Availability Overview

| Component        | HA Strategy                  |
| ---------------- | ---------------------------- |
| Proxmox Cluster  | 2-node cluster + DR standby  |
| Active Directory | 2x DCs (Delhi + Bangalore)   |
| Firewall         | FortiGate A-P Pair in Delhi  |
| DB Layer         | RDS + Local failover         |
| NAS              | Rsync to DR & S3             |
| LMS              | Load-balanced EC2 (optional) |

---

## 🧪 Example Failover Flow

```text
1. Power outage in New Delhi
2. FortiGate reroutes traffic via Bangalore VPN
3. Proxmox DR node powers on critical VMs (LMS, DNS, AD)
4. Users reconnect to replicated DB & file server
5. Monitoring alerts sent via Zabbix + MS Teams webhook
6. After recovery, data syncs back via Rsync and DB recovery
```

---

## ✅ Checklist for Server Architecture

* [x] DCs with Azure AD Sync
* [x] Local & cloud-hosted LMS
* [x] Scalable File + DB system
* [x] DR & backup in separate city
* [x] Core services Dockerized
* [x] Monitoring, logging, and alerting

---
