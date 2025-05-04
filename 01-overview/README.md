# 01 - Project Overview

## 🎯 Objective
Design a hybrid infrastructure for a mid-to-large-sized university environment that includes:

- Multi-campus connectivity
- On-premises data centers with virtualized infrastructure
- Cloud services for web, identity, and collaboration
- Secure device and endpoint management
- Backup, DR, and monitoring solutions
- Support for staff, faculty, students, and researchers
  
---

## 🧠 **Hybrid University Infrastructure Stack (Best-Practice Blueprint)**

> 🎯 Target: Medium-large university (multi-campus), hybrid environment, with both on-prem and cloud-hosted services. Follows standard practices used in modern enterprise education IT.

---

### 1. 🌐 **Network & Connectivity**

| Category            | Default Stack                                                                     |
| ------------------- | --------------------------------------------------------------------------------- |
| **Firewalls**       | FortiGate 200D (HQ), FortiGate 100D (branches)                                    |
| **Switches**        | Cisco SG500-28P (core), Cisco SG300-52 (edge)                                     |
| **Wi-Fi**           | UniFi 6 Professional for high-density APs                                         |
| **Routers/Gateway** | GPON H660GM-A + Archer C6 for branch                                              |
| **Topology**        | VLAN segmentation (e.g., Staff, Student, Guest, Printer), Inter-VLAN Routing, NAT |
| **DNS/DHCP**        | Windows Server 2019 (AD DNS, DHCP Failover)                                       |
| **VPN**             | FortiClient VPN (for staff remote access)                                         |

---

### 2. ☁️ **Cloud Platform Usage**

| Platform  | Usage                                                                                            |
| --------- | ------------------------------------------------------------------------------------------------ |
| **AWS**   | Web hosting (LMS), EC2 (lab servers), S3 (offsite backup), RDS (PostgreSQL for app DBs), Route53 |
| **Azure** | Azure AD (SSO + Identity), Microsoft 365, Intune, Azure Backup                                   |
| **GCP**   | Firebase for student app, Google BigQuery for analytics (optional)                               |

---

### 3. 🖥️ **On-Premises Systems**

| Component           | Version / Tool                                      |
| ------------------- | --------------------------------------------------- |
| **Domain Services** | Windows Server 2019 (AD DS, DNS, DHCP)              |
| **File & Print**    | Windows Server 2019 File Server with DFS + Quotas   |
| **Linux Servers**   | Ubuntu Server 22.04 LTS (for apps, Zabbix, Graylog) |
| **Hypervisor**      | Proxmox VE 8.1 (preferred over ESXi for cost)       |
| **Storage**         | TrueNAS CORE (NFS/SMB shares, ZFS replication)      |
| **Patch Mgmt**      | WSUS + PDQ Deploy Free                              |

---

### 4. 🛡️ **Security & Endpoint Protection**

| Security Component   | Stack                                            |
| -------------------- | ------------------------------------------------ |
| **Antivirus/EDR**    | Microsoft Defender for Endpoint (via Intune)     |
| **Drive Encryption** | BitLocker (Windows), LUKS (Linux)                |
| **Web Protection**   | FortiGuard DNS filtering                         |
| **DLP & Email Sec**  | Microsoft Purview (DLP), Defender for Office 365 |
| **SSL/DNS Security** | Let’s Encrypt (certs), Cloudflare (CDN, DDoS)    |

---

### 5. 💻 **Endpoint & Device Management**

| Component              | Default Stack                                |
| ---------------------- | -------------------------------------------- |
| **Windows Management** | Microsoft Intune (via Azure AD join)         |
| **Inventory**          | GLPI with FusionInventory                    |
| **Remote Support**     | AnyDesk or RustDesk (open source)            |
| **Mac/Linux**          | Munki or Ansible for Macs, Cockpit for Linux |

---

### 6. 💾 **Backup & Disaster Recovery**

| Component             | Stack                                                   |
| --------------------- | ------------------------------------------------------- |
| **VM/Server Backup**  | Veeam Community Edition (up to 10 workloads)            |
| **File-Level Backup** | Rsync + Rclone (for cloud sync to AWS S3)               |
| **Cloud-Native**      | AWS Backup (for EC2, RDS), Azure Backup (for M365)      |
| **DR Site**           | Cold standby Proxmox node, offsite S3 replication       |
| **RTO/RPO**           | RTO: 4 hours (critical), RPO: 1 hour (critical systems) |

---

### 7. 📈 **Monitoring, Logging & Alerts**

| Tool                     | Purpose                      |
| ------------------------ | ---------------------------- |
| **Zabbix 6.4 LTS**       | Server/Network monitoring    |
| **Grafana + Prometheus** | Dashboards & visualization   |
| **Graylog 5**            | Centralized log management   |
| **AWS CloudWatch**       | For AWS resources monitoring |

---

### 8. 🗃️ **Internal Core Applications**

| Category               | Tool                                           |
| ---------------------- | ---------------------------------------------- |
| **LMS**                | Moodle 4.3 (self-hosted on AWS EC2)            |
| **File Collaboration** | SharePoint Online (staff), OneDrive (students) |
| **Intranet Wiki**      | BookStack (self-hosted)                        |
| **Video Conferencing** | Zoom Business with EDU Plan                    |
| **Webmail**            | Microsoft Exchange Online (via M365)           |
| **Internal Git**       | Gitea (self-hosted) or GitHub EDU org          |

---

### 9. 🧪 **Application Stack for Dev / Students**

| Layer         | Stack                                 |
| ------------- | ------------------------------------- |
| **Frontend**  | React.js + TailwindCSS                |
| **Backend**   | Django (Python 3.11) or Node.js (v20) |
| **Database**  | PostgreSQL 15 (AWS RDS)               |
| **Container** | Docker + Docker Compose               |
| **CI/CD**     | GitHub Actions or Jenkins (optional)  |

---

### 10. 🧑‍💻 **Identity & Access**

| Component        | Stack                                             |
| ---------------- | ------------------------------------------------- |
| **Primary Auth** | Windows AD (sync with Azure AD)                   |
| **Cloud Auth**   | Azure AD (SSO for all apps, incl. Moodle, GitHub) |
| **Federation**   | Google Workspace for student login (optional)     |
| **2FA**          | Microsoft Authenticator or Google Authenticator   |

---

### 11. 🧰 **Automation & Configuration Management**

| Category                | Tool                                             |
| ----------------------- | ------------------------------------------------ |
| **IAC (Infra as Code)** | Terraform 1.6 (for AWS/Azure infra provisioning) |
| **Config Management**   | Ansible 8.5                                      |
| **Scripting**           | PowerShell 7 (Windows), Bash (Linux)             |
| **Task Scheduling**     | Systemd timers (Linux), Task Scheduler (Windows) |

---

### 12. 🏢 **Data Center & Physical Setup**

| Component          | Details                                             |
| ------------------ | --------------------------------------------------- |
| **Rack & Power**   | 42U rack, dual PDU, UPS (APC 2kVA)                  |
| **Cooling**        | AC with temp alert sensor                           |
| **Server Layout**  | Top-down: Switch > Router > Firewall > NAS > Server |
| **Patch Panels**   | Labeled + color-coded                               |
| **Access Control** | RFID lock + CCTV                                    |

---

### 13. 📚 **Documentation & ITSM**

| Purpose         | Tool                                             |
| --------------- | ------------------------------------------------ |
| **Docs/KB**     | MkDocs (with Material theme)                     |
| **Diagrams**    | Draw\.io / Diagrams.net                          |
| **Ticketing**   | GLPI or Freshservice                             |
| **IT Policies** | Stored in GitHub repo with Markdown & PDF export |

---

## 📌 Architecture Summary
A hybrid mix of centralized domain services, remote-access capabilities, campus-level segmentation, and secure internet-facing cloud services for apps and collaboration.
