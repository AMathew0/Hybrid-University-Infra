# 🌍 Multi-Site Network Setup Overview – Hybrid University

This document outlines the multi-site setup for Hybrid University, with three sites: **India HQ**, **Dubai Branch**, and **India Branch**. The setup includes detailed information about network segmentation, IP addresses, VLANs, and domain structure. This configuration is designed to provide high availability, performance, and security across all sites, with seamless integration into the hybrid cloud architecture (GCP, Azure, Microsoft 365).

---

## 🏢 Sites Overview

| Site Name        | Location      | Network Connectivity | Key Services                                           |
|------------------|---------------|----------------------|--------------------------------------------------------|
| India HQ         | New Delhi, India  | High-Speed MPLS      | AD DS, DNS, File Server, Print Services                |
| Dubai Branch     | Dubai, UAE   | MPLS, Site-to-Site VPN | AD DS, File Server, Print Services, Apps (Linux)       |
| India Branch     | Bangalore, India | MPLS, Site-to-Site VPN | AD DS, File Server, Apps (Linux, Zabbix, Graylog)      |

---

## 🔌 Network Segmentation

Each site uses VLANs to separate traffic and ensure security and performance. Here is a breakdown of the VLANs for each site:

| VLAN ID | Purpose              | Subnet                     | DHCP Range                | Site            |
|---------|----------------------|----------------------------|---------------------------|-----------------|
| 10      | Admin Network         | `192.168.10.0/24`          | `192.168.10.100 – 192.168.10.200` | India HQ        |
| 20      | Staff Network         | `192.168.20.0/24`          | `192.168.20.100 – 192.168.20.200` | India HQ        |
| 30      | Student Network       | `192.168.30.0/24`          | `192.168.30.100 – 192.168.30.200` | India HQ        |
| 40      | Admin Network         | `192.168.40.0/24`          | `192.168.40.100 – 192.168.40.200` | Dubai Branch    |
| 50      | Staff Network         | `192.168.50.0/24`          | `192.168.50.100 – 192.168.50.200` | Dubai Branch    |
| 60      | Student Network       | `192.168.60.0/24`          | `192.168.60.100 – 192.168.60.200` | Dubai Branch    |
| 70      | Admin Network         | `192.168.70.0/24`          | `192.168.70.100 – 192.168.70.200` | India Branch    |
| 80      | Staff Network         | `192.168.80.0/24`          | `192.168.80.100 – 192.168.80.200` | India Branch    |
| 90      | Student Network       | `192.168.90.0/24`          | `192.168.90.100 – 192.168.90.200` | India Branch    |

### Notes:
- Each branch uses **MPLS** for high-performance connectivity and **Site-to-Site VPN** for redundancy and secure communication between sites.
- VLANs are configured for segregation of traffic and to improve security, reducing the risk of unauthorized access to sensitive resources.
- Each site has its own dedicated **DHCP range** to ensure proper IP allocation.

---

## 🌐 Domain Configuration

The **Active Directory (AD)** domain is set up for centralized identity management across all sites. 

### Domain Information:
- **Domain Name**: `hybriduniversity.local`
- **Forest Functional Level**: Windows Server 2016+
- **Global Catalog Servers**: Primary in India HQ, secondary in Dubai and India Branches
- **FSMO Roles**: Hosted on the Primary DC in India HQ
- **Active Directory Sites and Services**:
  - **India HQ**: Default site, holds all FSMO roles
  - **Dubai Branch**: Secondary DC and Global Catalog server
  - **India Branch**: Secondary DC and Global Catalog server
- **Replication**:
  - **Site-to-Site Replication**: Between India HQ and Dubai Branch
  - **Site-to-Site Replication**: Between India HQ and India Branch
  - **DFS Replication**: For shared files between all branches

---

## 🔄 VPN Connectivity

The VPN between the sites is established using **Site-to-Site VPN** technology, providing encrypted tunnels for secure communication. Below is the configuration for each site:

1. **India HQ → Dubai Branch**:
   - VPN Type: **IPSec VPN**
   - Encryption: **AES 256**
   - Tunnel IP: `203.0.113.1` (India HQ), `198.51.100.1` (Dubai Branch)
   - IP Ranges: `192.168.10.0/24` (India HQ), `192.168.40.0/24` (Dubai Branch)
   
2. **India HQ → India Branch**:
   - VPN Type: **IPSec VPN**
   - Encryption: **AES 256**
   - Tunnel IP: `203.0.113.2` (India HQ), `203.0.113.3` (India Branch)
   - IP Ranges: `192.168.10.0/24` (India HQ), `192.168.70.0/24` (India Branch)

### Notes:
- **High Availability**: VPN tunnels are configured for redundancy with automatic failover.
- **Traffic Filtering**: Only necessary traffic is allowed between the branches to minimize potential security threats.

---

## 🛡️ Network Security Measures

The following security measures are implemented across all sites to ensure a secure and robust network environment:

### 1. **Firewalls**:
- Use of **hardware firewalls** and **VPC firewalls** to secure communication between sites.
- Specific rules are applied to allow only the necessary ports and protocols for each VLAN and inter-site communication.

### 2. **Network Access Control**:
- **Port security** is configured on switches to limit the number of MAC addresses per port.
- **802.1X authentication** is enabled on wireless access points for device security.

### 3. **Intrusion Detection System (IDS)**:
- **Zabbix** is configured for monitoring network traffic and detecting potential intrusions across sites.

---

## 🛠️ Infrastructure Services by Site

### **India HQ**:
- **DC1**: Domain Controller, DNS, DHCP, File Services
- **DC2**: Backup Domain Controller, Print Services
- **Proxmox VE**: Host for virtualization
- **TrueNAS CORE**: Storage for file shares and DFS replication
- **Patch Management**: WSUS for Windows updates, PDQ Deploy for software

### **Dubai Branch**:
- **DC1**: Domain Controller, DNS, DHCP
- **Proxmox VE**: Virtualization for application hosting
- **TrueNAS CORE**: Storage replication with India HQ

### **India Branch**:
- **DC1**: Domain Controller, DNS, DHCP
- **Proxmox VE**: Virtualization for app hosting (Zabbix, Graylog)
- **TrueNAS CORE**: Local file storage for faculty and staff

---

## ✅ Summary Table

| Site Name        | Key Services                      | Network Configuration             | VPN Connectivity   |
|------------------|-----------------------------------|-----------------------------------|---------------------|
| **India HQ**     | AD DS, DNS, DHCP, File Services   | VLANs: 10, 20, 30, Static IPs     | Site-to-Site VPN to Dubai and India Branch |
| **Dubai Branch** | AD DS, File Services, Print Server| VLANs: 40, 50, 60, Static IPs     | Site-to-Site VPN to India HQ and India Branch |
| **India Branch** | AD DS, File Services, Zabbix      | VLANs: 70, 80, 90, Static IPs     | Site-to-Site VPN to India HQ and Dubai Branch |

---

## 💼 Conclusion

This multi-site setup provides **high availability, redundancy**, and secure connectivity between Hybrid University's main sites. By leveraging advanced networking, segmentation, and site-specific services, we ensure smooth operations across locations while keeping performance and security top of mind.
