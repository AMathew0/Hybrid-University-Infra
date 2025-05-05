# 🏢 Multi-Site Data Center Protocols & Physical Setup

* Protocol breakdown
* Example configuration flows
* Tables for IPsec, BGP, Rsync, SAML/OAuth2
* VLAN segmentation
* HA/DR strategies
* Device inventory

---

````markdown

This document details the core networking protocols, technologies, high availability (HA), disaster recovery (DR) strategies, and example flows used in a **multi-site hybrid university data center environment**.

````
---

## 🔌 Protocols Used & Purpose

| Protocol         | Used For                  | Tools/Notes                    |
|------------------|---------------------------|---------------------------------|
| IPsec VPN        | Site-to-site secure tunnel| FortiGate (HA) or OpenVPN       |
| BGP              | Dynamic Routing           | Edge routers with redundancy    |
| Rsync over SSH   | File Sync between sites   | NAS-to-NAS + AWS S3 offload     |
| SAML / OAuth2    | Identity Federation       | Azure AD, Google Workspace SSO  |

---

## 🧪 Example Setup Flow

```text
1. Deploy physical rack, switches, firewall, NAS, servers
2. Configure VLANs per department (Mgmt, Staff, Guest, IoT)
3. Set up Proxmox Cluster (2 nodes) + 1 cold standby at DR site
4. Install NAS with RAID 6 → Rsync to second NAS + AWS S3
5. Configure FortiGate VPN between Site 1 and Site 2
6. Set up Zabbix for full-site monitoring (compute, network, power)
7. Enable Graylog log collection from FortiGate, AD, and servers
8. Simulate DR failover: power off Site 1, bring up Site 2 VMs
````

---

## 🔐 IPsec VPN – Site Interconnect

| Site A            | Site B          | VPN Device     | Tunnel Type | Encryption | Failover          |
| ----------------- | --------------- | -------------- | ----------- | ---------- | ----------------- |
| DC1 (Main Campus) | DC2 (DR/Remote) | FortiGate 200D | IPsec VPN   | AES-256    | Manual + Scripted |

**Example**:

* VPN Gateway: `fw1.dc1.hybrid.edu` → `fw1.dc2.hybrid.edu`
* Tunnel Interface: `vpn_dc1_dc2_01`
* Routing: Static + BGP failover fallback
* Monitoring: Auto-reconnect script every 5 minutes

---

## 🔄 BGP Routing

| Location | Router Type      | ASN   | Peers                  | Protocol              |
| -------- | ---------------- | ----- | ---------------------- | --------------------- |
| DC1      | Cisco SG500 (HA) | 64510 | DC2 Router, ISP1, ISP2 | BGP + static failback |
| DC2      | FortiGate        | 64520 | DC1 Router, Local ISP  | BGP route inject      |

* Ensures automatic route reconfiguration during link failure
* BGP weights used for preferred path selection
* BGP monitored via Zabbix ping + reachability check

---

## 📂 Rsync over SSH – File Sync & Offsite Backup

| Source NAS | Destination         | Type           | Frequency | Encryption    |
| ---------- | ------------------- | -------------- | --------- | ------------- |
| DC1 NAS-01 | DC2 NAS-01          | Rsync over SSH | Hourly    | SSH (port 22) |
| DC1 NAS-01 | AWS S3 (via Rclone) | Rclone (CLI)   | Daily     | HTTPS         |

```bash
# Example: Sync to secondary NAS
rsync -avz -e "ssh -i /path/key" /mnt/storage/ user@nas2:/mnt/storage/

# Example: Sync to cloud S3
rclone sync /mnt/storage/ aws-s3:hybrid-backup --progress
```

---

## 👥 Identity Federation – SAML / OAuth2

| Platform         | Auth Protocol | Provider          | Used For                   |
| ---------------- | ------------- | ----------------- | -------------------------- |
| Moodle LMS       | SAML          | Azure AD          | Staff and Faculty SSO      |
| GitHub EDU Org   | OAuth2        | Azure AD          | Developers and Students    |
| Google Workspace | OAuth2        | Google Federation | Students (Gmail, Calendar) |

* MFA via Microsoft Authenticator (enforced for staff)
* Group-based access using AD Groups (IT, Dev, HR)
* Conditional Access policies restrict login from unknown devices

---

## 🧩 VLAN Segmentation (Per Department)

| VLAN ID | Department  | Subnet        | Example Devices          |
| ------- | ----------- | ------------- | ------------------------ |
| 10      | Management  | 10.10.10.0/24 | Servers, Admin PCs       |
| 20      | Staff       | 10.10.20.0/24 | Office laptops           |
| 30      | Students    | 10.10.30.0/24 | BYOD, Student Laptops    |
| 40      | IoT/Devices | 10.10.40.0/24 | CCTV, RFID, HVAC sensors |

* VLANs isolated via L3 firewall rules on FortiGate
* DHCP/DNS handled centrally with DHCP relay
* Logging per VLAN via Graylog for compliance

---

## 🔁 High Availability & DR Strategy

| Component   | HA Method       | DR Method                   |
| ----------- | --------------- | --------------------------- |
| Firewall    | Active-Passive  | Manual promote at DR site   |
| Proxmox     | Clustered       | Cold standby in DC2         |
| NAS Storage | Dual NAS        | Rsync + AWS S3 replication  |
| VPN Tunnel  | Redundant IPsec | Monitored + restart scripts |

* **Failover Time (RTO)**: 4 hours max (critical systems)
* **Backup Interval (RPO)**: 1 hour (DB + storage systems)
* DR simulations every 6 months to test failover procedure

---

## 🖥️ Devices & Infrastructure Inventory

| Device/Component | Count | Model                | Description                  |
| ---------------- | ----- | -------------------- | ---------------------------- |
| Firewall         | 2     | FortiGate 200D       | Site-to-site VPN, Web Filter |
| Core Switch      | 2     | Cisco SG500-28       | L3 Routing, VLAN Trunking    |
| NAS              | 2     | QNAP TS-873A         | RAID6, Rsync-enabled Storage |
| Virtualization   | 3     | Dell R720 + Proxmox  | 2-node + 1 DR cold standby   |
| UPS              | 2     | APC 2kVA             | Power backup                 |
| Patch Panels     | 1     | 24-Port Cat6         | Color-coded + labeled        |
| CCTV & RFID      | 1 set | HikVision / Ubiquiti | Entry control, compliance    |
| Monitoring       | 1     | Zabbix 6.4 LTS       | All infra health + alerts    |
| Logging          | 1     | Graylog 5            | Central log management       |

---

## ✅ Summary

This multi-site design ensures:

* **End-to-end encryption** for inter-site communication
* **HA and DR** strategies with minimal RTO/RPO
* **Modern identity federation** with granular role access
* **Automated backups** and active monitoring systems
* **Isolation by VLAN**, full log visibility, and fault-tolerant connectivity
  
```
