# 🏢 Multi-Site Data Center & Physical Setup

## 🗺️ Site Locations
- **Primary Site (Site 1)**: Located in Region A (Main Business Operations)
- **Secondary Site (Site 2)**: Located in Region B (Disaster Recovery, Offsite Backup)
- **Tertiary Site (Optional - Site 3)**: Located in Region C (Backup or Secondary Disaster Recovery Site)

## ⚡ Power & Rack Configuration
### **Power Setup**
- **Dual PDUs (Power Distribution Units)** for each site to ensure power redundancy.
- **UPS (Uninterruptible Power Supply)** installed at each site to provide backup during power outages:
  - **APC 2kVA UPS** for each data center.
  - **On-Site Backup Generators**: To ensure power during extended outages (only if required).
  
### **Rack Configuration**
- **42U Rack** in each site.
- **Top-Down Layout**:
  - **Site 1**: Switch > Router > Firewall > NAS > Server
  - **Site 2**: Switch > Router > Firewall > NAS > Server
  - **Site 3**: Optional for further backup capacity or load balancing.

## ❄️ Cooling & Environmental Monitoring
- **Environmental Control Systems** (AC + Humidity Control) in all sites.
- **Temperature Sensors** installed in each data center, integrated with centralized monitoring tools like **Zabbix**.
- **Cooling Failover**: Redundant AC units to ensure cooling in case of failure.
- **Environmental Alerts**: Zabbix or Grafana to trigger alerts for abnormal temperature or humidity levels.

## 🔄 Data Replication & Backup
### **Data Replication Strategy**
- **Synchronous Replication** for critical applications and databases between Site 1 and Site 2 (real-time replication).
  - **AWS S3 Cross-Region Replication** for storing critical files in both primary and secondary locations.
  - **Database Replication** (e.g., **PostgreSQL**, **MySQL**) to ensure data is consistent across all sites.
  
### **Backup Strategy**
- **Primary Site (Site 1)**: Local backup solutions like **Veeam** and **Rsync**.
- **Secondary Site (Site 2)**: Offsite cloud backup using **AWS Backup** or **Azure Backup**.
- **Disaster Recovery Site** (Site 3, optional): A cold standby backup server that can take over in case Site 1 or Site 2 fails.
- **Backup Frequency**: Critical systems have an RPO (Recovery Point Objective) of 1 hour, with RTO (Recovery Time Objective) of 4 hours.
  
### **Offsite Backup & Cloud Storage**
- **Rsync** + **Rclone** for syncing data to cloud storage (e.g., **AWS S3** or **Azure Blob Storage**).
  
## 🌐 Network Redundancy
### **Networking Setup**
- **Multi-Site VLANs** configured to segment traffic across different sites securely.
- **SD-WAN** or **Cross-Site VPN** for secure communication between sites.
  - **VPN Failover**: In case one site loses connectivity, VPN traffic is automatically routed to the next available site.
  - **BGP (Border Gateway Protocol)** for dynamic routing and failover.

### **Load Balancing**
- Use **HAProxy** or **NGINX** for distributing traffic across sites (active-active or active-passive configurations).
- **Anycast IPs** for seamless DNS failover, directing users to the nearest data center.
  
### **Inter-Site Connectivity**
- **High-Speed Fiber Links** between the primary and secondary data centers for low-latency, high-bandwidth connectivity.
- **Backup Communication Links** (e.g., 4G/5G LTE for emergency access).

## 🛡️ Access Control & Security
### **Access Control Systems**
- **Centralized RFID Access System** at each site to control physical access to racks and server rooms.
- **CCTV** monitoring in each data center for security.
- **24/7 Security Personnel** for physical site monitoring.
  
### **Firewalls & Security**
- **FortiGate 200D** firewalls at each site for network perimeter security.
- **Intrusion Detection Systems** (IDS) to monitor malicious activities in all locations.
- **Cross-Site Security Monitoring**: Tools like **Splunk** or **Graylog** to collect and correlate security logs from all data centers.

### **Multi-Factor Authentication (MFA)**
- **Azure AD MFA** for administrative and remote access to the infrastructure.
- **Google Authenticator** or **Microsoft Authenticator** used for staff and admin accounts.

## 🔐 Disaster Recovery & Failover
### **Disaster Recovery Site (Site 2)**
- **Cold Standby Site**: Proxmox or VMware node setup for quick activation in case of disaster at the primary site.
- **Failover Testing**: Regular failover drills between Site 1 and Site 2 to ensure RTO and RPO targets are met.

### **Continuous Data Availability**
- **Site Failover**: In case of a primary site failure, workloads automatically failover to the secondary site, ensuring no downtime.
- **Site Restoration**: Once the primary site is restored, data syncs automatically from the secondary site to maintain consistency.

## 🌍 Global Monitoring & Alerts
- **Centralized Monitoring** using **Zabbix**, **Grafana**, and **Prometheus**.
  - **Centralized Log Aggregation** using **Graylog** or **Splunk** to monitor logs across all sites.
  - **Real-Time Monitoring** of hardware health, network connectivity, and resource utilization in each data center.
  
## 💾 Backup & Storage
- **Storage Setup**: Redundant storage at each site using **NAS (Network Attached Storage)** and **SAN (Storage Area Network)** configurations.
- **Cloud Storage**: Backup data is replicated to cloud storage services like **AWS S3** or **Azure Blob Storage** to ensure offsite availability.

## 📦 Server and Application Failover
- **VMware/Proxmox Clusters** for server failover between sites.
- **Database Replication** for high availability: e.g., **MySQL** or **PostgreSQL** replication.
- **File Syncing**: **Rsync** to sync files between primary and secondary sites.

---

### Example of Deployment & Configuration Flow

1. **Primary Site Deployment**:
   - Deploy servers, storage, and networking components.
   - Configure **VLANs**, **VPNs**, **SD-WAN**, and **firewalls**.
   - Set up **Backup Solutions** and **Data Replication**.

2. **Secondary Site Setup**:
   - Mirror configurations from the primary site.
   - Ensure data replication (AWS S3, RDS).
   - Test failover capabilities.

3. **Tertiary Site (Optional)**:
   - Similar setup as secondary site with minimal resources, ready for failover.
   - Sync backup data to this site for high availability.

---

This setup ensures that  **multi-site data centers** are equipped with all the necessary components for **high availability**, **disaster recovery**, and **business continuity**, ensuring no downtime and data loss across sites.

# 🏢 Multi-Site Data Center & Physical Setup - Deep Dive

A resilient and redundant multi-site infrastructure setup ensuring high availability (HA), disaster recovery (DR), security, and efficient operations for a hybrid cloud and on-premises environment.

---

## 🗂️ Domain-Wise Technology Stack & Protocols

| Domain                 | Tech Stack / Devices                                     | Protocols/Standards              | Purpose / Notes                                  |
|------------------------|----------------------------------------------------------|----------------------------------|--------------------------------------------------|
| Compute Infrastructure | Proxmox VE, VMware ESXi                                  | KVM/QEMU, vSphere                | VM hosting, clustering across sites              |
| Storage                | Synology NAS x2/site, AWS S3 (Cross-Region)              | NFS, SMB, iSCSI, HTTPS           | Local storage + cloud backup & sync              |
| Backup                 | Veeam (CE), Rsync, Rclone, AWS Backup, Azure Backup      | FTP, SSH, HTTPS, API             | VM & file backup; DR sync to cloud               |
| Networking             | Cisco SG Switches, Netgear FSM, UniFi 6 Pro, FortiGate   | VLAN, SNMP, OSPF/BGP, VPN        | Core LAN/WAN with segmentation and failover      |
| Monitoring             | Zabbix, Prometheus, Grafana, CloudWatch                  | ICMP, SNMP, HTTP, HTTPS          | Performance, health, and alerting                |
| Logging                | Graylog, Fluentd, syslog-ng                              | Syslog, GELF, REST API           | Centralized log aggregation & correlation        |
| Access Control         | RFID Panel, CCTV (Site), Azure AD + MFA                 | RADIUS, LDAP, RTSP               | Physical + logical access management             |
| Cloud Integration      | AWS, Azure (Hybrid AD Join), GCP (SAML Federation)       | HTTPS, SAML, OAuth2              | Identity federation and cloud app integration    |
| Endpoint Management    | Microsoft Intune, Munki, Cockpit, Ansible                | HTTPS, SSH, WinRM, WMI           | Device provisioning, patching, automation        |
| Dev Stack              | Docker, GitHub Actions, Jenkins, PostgreSQL              | CI/CD, REST APIs, DB access      | Student & staff development/testing environments |

---

## 🖥️ Devices & Hardware Count (Per Site)

| Device Type         | Model / Tool            | Count/Site | HA Methodology                 | Notes                                |
|---------------------|--------------------------|------------|-------------------------------|--------------------------------------|
| Rack                | 42U APC / Equiv.         | 1          | N/A                           | Full-size, locked                    |
| UPS                 | APC Smart-UPS 2kVA       | 2          | Redundant inline power        | One per PDU                          |
| PDU                 | APC Metered PDU          | 2          | Redundant per rack            | Dual feed from UPS                   |
| Switches            | Cisco SG500 / SG300      | 2          | LAG/Stacking                  | Core switching                       |
| Routers             | Tenda / MikroTik         | 1-2        | Failover routing              | WAN load balancing                   |
| Firewalls           | FortiGate 100D/200D      | 2          | Active-Passive (HA Pair)      | Stateful failover setup              |
| Wi-Fi Access Points | UniFi 6 Pro              | 3–5        | Controller-based              | Staff & guest VLANs                  |
| Servers             | Dell R720 / HP DL380     | 2–3        | Proxmox HA Cluster            | VM host redundancy                   |
| NAS Storage         | Synology DS920+          | 2          | RAID + Rsync to DR site       | Local + DR file copy                 |
| Backup Server       | Ubuntu (Rsync, Veeam)    | 1          | Rsync + Cloud Upload          | Cold standby                         |
| CCTV Cameras        | Hikvision / Reolink      | 6–8        | NVR w/ RAID                   | Remote access, motion detect         |
| RFID Controller     | RFID ZKTeco / HID        | 1          | Integrated to DC door         | Access logging via AD                |

---

## 🛡️ High Availability (HA) Methodologies

| Area              | HA Approach                            | Description |
|-------------------|-----------------------------------------|-------------|
| Network           | Dual switches, VPN + SD-WAN             | Ensures path failover and segmentation |
| Compute           | Proxmox cluster, HA VMs                 | Live failover on hardware failure     |
| Storage           | RAID-5/6 on NAS, Rsync to secondary NAS | Redundant storage with async sync     |
| Firewall          | FortiGate HA Pair                       | Stateful failover & sync config       |
| Power             | Dual PDU, UPS                           | No single point of failure            |
| Cloud Apps        | Hosted on AWS/Azure w/ auto-scaling     | Cloud-native HA                       |

---

## ☁️ Disaster Recovery (DR) Strategies

| Layer             | DR Component                         | Strategy Type           | Frequency     |
|-------------------|--------------------------------------|-------------------------|---------------|
| VMs               | Veeam CE Backup                      | Daily backup to NAS     | Daily         |
| Files             | Rsync to offsite NAS + Rclone to S3  | Incremental cloud sync  | Hourly        |
| Database          | PostgreSQL Streaming Replication     | Active-passive          | Real-time     |
| DNS Failover      | Cloudflare w/ geo-load balancer      | Failover routing        | Instantaneous |
| DR Site (Cold)    | Proxmox Node                         | Cold standby            | Manual spin-up|

---

## 🌐 Example Network Layout (Per Site)

```yaml

42U Rack Layout (Top → Bottom):
[U1–U2] UniFi Access Points (PoE)
[U3–U5] Cisco Core Switches (Stacked)
[U6–U8] Router + FortiGate Firewalls
[U9–U10] Patch Panel + Color Coded
[U11–U15] Synology NAS + UPS Battery Units
[U16–U30] Dell R720 / HP Servers
[U31–U42] Cold standby Proxmox + Free U positions

```

---

## 🔄 Site-to-Site Connectivity

| Protocol           | Used For                  | Tools/Notes                   |
|--------------------|---------------------------|--------------------------------|
| IPsec VPN          | Site-to-site secure tunnel| FortiGate or OpenVPN           |
| BGP                | Dynamic Routing           | Redundant routing table        |
| Rsync over SSH     | File Sync between sites   | Used for backup NAS replication|
| SAML / OAuth2      | Identity Federation       | Azure AD, Google Workspace     |

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

```

## 📋 Final Notes

```

🔒 All critical components (firewall, storage, AD) must have redundant counterparts
🎛️ Use color-coded cables and label everything on both ends
📶 Wi-Fi SSIDs should be consistent across sites with VLAN mapping
📧 Monitor all alerts centrally using Grafana dashboards and email/SMS alerts

```
This setup provides a robust foundation for a resilient multi-site infrastructure, integrating on-premises and hybrid cloud components efficiently.
