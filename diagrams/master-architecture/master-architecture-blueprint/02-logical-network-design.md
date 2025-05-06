## ✅ Logical Network Design – Multi-Site Hybrid University

---

**Logical blueprint**

* Core components per site
* IP subnetting & VLAN design
* Site-to-site connectivity (VPN)
* Routing & firewall zones
* Cloud integration points (Azure, AWS)

---

### 🧭 Multi-Site Logical Topology Overview

We assume **3 locations**:

| Location      | Purpose                                   | Connectivity Role            |
| ------------- | ----------------------------------------- | ---------------------------- |
| **New Delhi** | Primary Campus (HQ, LMS, Admin)           | Core site with full services |
| **Bangalore** | Research & Tech + Backup Site             | Secondary + DR site          |
| **Dubai**     | International Access, Lightweight hosting | Tertiary, VPN extension      |

---

### 🧱 Logical Network Diagram – Component Breakdown

#### 🧩 Per Site Core Components:

| Layer         | Devices / Services                              |
| ------------- | ----------------------------------------------- |
| **Edge**      | FortiGate 100D (HQ), FortiGate 60D (Others)     |
| **Routing**   | Static or BGP between sites                     |
| **Switching** | Cisco SG500 (L3), UniFi APs                     |
| **Compute**   | Proxmox Cluster, VMware VMs                     |
| **Storage**   | Synology NAS + Rsync + AWS S3                   |
| **Directory** | Windows AD + Azure AD Connect                   |
| **Apps**      | Moodle, ERP, Git, SharePoint, Zabbix, BookStack |

---

### 🌐 IP Addressing & VLAN Plan (Example)

| VLAN ID | VLAN Name  | IP Range      | Purpose                   |
| ------- | ---------- | ------------- | ------------------------- |
| 10      | Management | 10.10.10.0/24 | Switches, Firewalls, UPS  |
| 20      | Staff      | 10.10.20.0/24 | Faculty, Admin PCs        |
| 30      | Students   | 10.10.30.0/24 | Lab PCs, BYOD             |
| 40      | Servers    | 10.10.40.0/24 | LMS, AD, NAS, Proxmox     |
| 50      | Guest      | 10.10.50.0/24 | Internet-only VLAN        |
| 99      | Transit    | 10.10.99.0/24 | VPN/BGP Peering, HA links |

➡️ Repeat similar subnetting per site, e.g., Bangalore = `10.20.x.x`, Dubai = `10.30.x.x`

---

### 🔐 Inter-Site VPN Design

| Site A    | Site B    | Protocol  | Tool              | Mode        | Redundancy        |
| --------- | --------- | --------- | ----------------- | ----------- | ----------------- |
| New Delhi | Bangalore | IPsec VPN | FortiGate IPsec   | Route-based | Dual-WAN          |
| New Delhi | Dubai     | IPsec VPN | FortiGate/OpenVPN | Route-based | SD-WAN (optional) |
| Bangalore | Dubai     | IPsec VPN | FortiGate         | Route-based | Optional          |

---

### 📡 Internet + Cloud Access

| Resource                 | Method                                | Comments                                |
| ------------------------ | ------------------------------------- | --------------------------------------- |
| Microsoft 365            | Azure AD Connect from Delhi Site      | Sync local AD, enable SSO + MFA         |
| AWS (S3, EC2, RDS)       | Direct Internet or NAT Gateway        | Access from Proxmox/Django via S3 + RDS |
| Moodle LMS (Self-hosted) | Hosted in Delhi + backup in Bangalore | DNS failover or Cold Standby via Rsync  |
| Zoom, GitHub, etc.       | Direct Cloud over Internet            | Secured via conditional access          |

---

### 🚧 Example Zoning & Firewall Design (FortiGate)

| Zone           | Traffic Allowed To         | Notes                        |
| -------------- | -------------------------- | ---------------------------- |
| LAN-Staff      | LAN-Servers, Internet      | AD-authenticated             |
| LAN-Students   | Internet only, LMS limited | Guest control via MAC/802.1X |
| LAN-Management | All Zones                  | For IT Ops only              |
| VPN-Zone       | LAN-Servers, AD, S3 Backup | Used in DR or sync jobs      |

---

### 📦 Example High-Level Flow: LMS Access (Student)

```text
1. Student → WiFi (VLAN 30, 10.10.30.10)
2. DNS resolves lms.hybriduniversity.edu to EC2 instance IP
3. FortiGate routes request → VPN or Internet (based on location)
4. Azure AD signs in user (SSO via SAML)
5. Session created, access LMS content on RDS/PostgreSQL
```

---

### 🛡️ DR & HA Strategy

| Component     | Primary Site | DR Strategy                     | Tool                |
| ------------- | ------------ | ------------------------------- | ------------------- |
| AD/DC         | New Delhi    | Replica in Bangalore & Azure AD | AD Sites & Services |
| Moodle        | EC2 (Delhi)  | Snapshot + Rsync to DR site     | Rsync + Proxmox     |
| NAS Storage   | Synology     | Rsync + AWS S3                  | Rsync + Rclone      |
| Zabbix Server | Bangalore    | Agent fallback config           | Zabbix HA/Proxies   |
| DHCP/DNS      | On-prem AD   | Backup DHCP & DNS in Bangalore  | AD-integrated       |

---
