# 02 - Networking Design for Hybrid University Infrastructure

## 🧭 Objective
Design a secure, scalable, and highly available networking architecture connecting:
- On-premises data centers
- Multiple campuses/sites
- Cloud environments (AWS, Azure, GCP)
- Remote users (staff, faculty, students)

---

## 🌐 Network Topology

### 1. 🏫 On-Prem Campus LAN Design
- **Core Switch**: Cisco Catalyst 9500 (L3)
- **Distribution Layer**: Cisco SG500 Series (L2/L3)
- **Access Layer**: VLAN-based with port security
- **Wireless Access**: UniFi 6 Pro APs

### 2. 📡 WAN & Site-to-Site Connectivity
- **SD-WAN Gateway**: FortiGate 200D (Primary), FortiGate 100D (Secondary)
- **MPLS/Leased Line**: Redundant connections between campuses
- **VPN Tunnel**: IPsec-based Site-to-Site VPN across cloud and on-prem

### 3. ☁️ Cloud Connectivity
| Cloud  | Method                                  | Purpose                        |
|--------|-----------------------------------------|--------------------------------|
| AWS    | AWS Site-to-Site VPN, Transit Gateway   | S3, EC2, Lambda, RDS           |
| Azure  | Azure Virtual WAN + ExpressRoute (sim)  | AAD, SharePoint, Intune        |
| GCP    | Cloud VPN + VPC Peering                 | Internal App Hosting           |

---

## 🛡 Network Security Zones (Segmentation)
| Zone            | Description                          | Example VLANs |
|-----------------|--------------------------------------|---------------|
| DMZ             | Public-facing services               | 10.0.10.0/24  |
| Admin           | System Admin & IT Management         | 10.0.20.0/24  |
| Staff           | University Faculty/Staff             | 10.0.30.0/24  |
| Student         | Student Labs, BYOD                   | 10.0.40.0/24  |
| IoT             | Surveillance, Access Control         | 10.0.50.0/24  |

---

## 🗺 IP Addressing Strategy
- IPv4 Private Addressing (RFC1918)
- Static/DHCP-managed IP pools
- Reserved subnets for each campus and zone

---

## 🧪 High Availability & Redundancy
- **Link Aggregation (LACP)** between switches
- **Dual WAN uplinks with failover**
- **VRRP/HSRP** for gateway failover
- **DNS Failover using Route 53 / Azure Traffic Manager**

---

## 🛠 Network Services
- DNS: Windows Server DNS / AWS Route 53
- DHCP: Windows DHCP / Cisco Switch DHCP Helper
- NTP: Chrony (Linux) or external pool.ntp.org

---

## 🔒 Firewall & Access Control
- NGFW: FortiGate with UTM
- ACLs between VLANs and Zones
- Geo-blocking, deep packet inspection, and threat detection
- Cloud security groups (AWS SGs, Azure NSGs, GCP FW Rules)

---

## 📡 Remote Access
- FortiClient VPN for staff/faculty
- Azure AD Conditional Access for identity-based access
- MFA enforced (Azure/M365 + FortiToken)

---

## 📊 Monitoring & Logging
- SNMPv3 + NetFlow → Zabbix
- Syslog → Graylog or ELK Stack
- Traffic Analysis → ntopng, Wireshark (local)

---

## 📁 Diagram Reference
See `diagrams/networking-design.drawio` for a full architecture diagram.

## 📋 VLAN & IP Addressing Table

| VLAN ID | Name        | Subnet           | Description                          | DHCP Scope           |
|---------|-------------|------------------|--------------------------------------|-----------------------|
| 10      | DMZ         | 10.0.10.0/24     | Public-facing services               | Static / DHCP         |
| 20      | Admin       | 10.0.20.0/24     | System & Infrastructure Admins       | 10.0.20.50–150        |
| 30      | Staff       | 10.0.30.0/24     | University Faculty & Staff           | 10.0.30.50–200        |
| 40      | Student     | 10.0.40.0/24     | Student Labs & BYOD                  | 10.0.40.10–250        |
| 50      | IoT         | 10.0.50.0/24     | IP Cameras, Biometric Scanners       | Static                |
| 60      | Server Zone | 10.0.60.0/24     | Internal Application Servers         | Static                |
| 70      | Backup/DR   | 10.0.70.0/24     | Backup & Disaster Recovery Zone      | Static                |

> 🔐 **Tip:** Reserve `.1` for gateway, `.2–.10` for firewalls/load balancers.
---
