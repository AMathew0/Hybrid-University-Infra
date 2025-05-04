# 🔥 Firewall & VPN Security Strategy – Hybrid University

This document outlines the design and management of firewall and VPN solutions deployed across Hybrid University to safeguard on-campus and remote access infrastructure.

---

## 🌐 Objectives

- Enforce **network perimeter protection** using enterprise-grade firewalls.
- Secure **remote staff and faculty access** via VPN.
- Implement **segmented and isolated VLANs** for different departments and services.
- Ensure **deep packet inspection (DPI)** and intrusion prevention.

---

## 🛡️ Firewall Components

| Device         | Role                        | Deployment Zone     | Features Used                          |
|----------------|-----------------------------|----------------------|----------------------------------------|
| FortiGate 200D | Main perimeter firewall      | Central HQ          | IPS, DPI, Web Filtering, VLAN Routing  |
| FortiGate 100D | Branch/Secondary firewall    | Remote Campus        | VPN, NAT, Policy-Based Routing         |
| Cloudflare     | Web application firewall     | Public Web Services | DDoS Protection, DNS Proxy, TLS Certs  |

---

## 🧱 VLAN & Network Segmentation Design

To isolate traffic and enforce least privilege access, the network is divided into functional VLANs.

| VLAN ID | Department/Zone       | Subnet              | Description                          |
|---------|------------------------|----------------------|--------------------------------------|
| 10      | Management/Admin       | 10.0.10.0/24         | IT admins, core infrastructure       |
| 20      | Academic Staff         | 10.0.20.0/24         | Faculty and teaching staff           |
| 30      | Student Network        | 10.0.30.0/24         | Internet-limited network             |
| 40      | Servers                | 10.0.40.0/24         | Data, DNS, Zabbix, GitHub runners    |
| 50      | Wi-Fi Devices          | 10.0.50.0/24         | Mobile & BYOD devices                |
| 99      | Guest Wi-Fi            | 10.0.99.0/24         | Internet-only guest access           |

---

## 🔐 VPN Access – Faculty & Remote Admins

### 🔧 VPN Technologies Used

| VPN Type      | Technology     | Use Case                       |
|---------------|----------------|--------------------------------|
| Site-to-Site  | IPsec (FortiGate) | Secure campus interconnect     |
| Remote Access | SSL VPN        | Staff & Admin remote access    |

### 🛠️ VPN Policy Configuration (FortiGate)

- **Authentication**: Azure AD (via SAML) or local LDAP
- **Split Tunneling**: Disabled (all traffic routed via VPN)
- **User Groups**: `Admins`, `Faculty-VPN`, `IT-VPN`
- **Access Control**: VLAN-restricted per group

---

## 🔄 High Availability (HA)

- Primary/Secondary FortiGate pairs configured in **Active-Passive HA mode**
- **Automatic failover** enabled for seamless transition
- Monitored via **SNMP + Zabbix alerts**

---

## 🔍 Logging & Monitoring

- **FortiAnalyzer** or syslog forwarding to **Graylog**
- Firewall logs retained for 180 days
- VPN login logs integrated with **Microsoft Sentinel** for anomaly detection

---

## 📜 Backup & Configuration Management

- Daily auto-backups of firewall configurations to a secured internal share
- Monthly configuration audits stored in Git versioned repo (private)

---

## 📌 Best Practices

- Enforce **Geo-IP blocking** for unnecessary regions
- Regular **rule cleanup and audit** (quarterly)
- Use **Application Control** for web-based traffic shaping
- Enable **certificate inspection** for encrypted traffic analysis
- VLAN access controlled by **FortiGate inter-VLAN policies**

---

## ✅ Summary

| Feature                 | Tool / Method Used      |
|-------------------------|--------------------------|
| Perimeter Firewall      | FortiGate 200D, 100D     |
| VPN Access              | FortiGate SSL/IPsec VPN  |
| Web App Firewall        | Cloudflare WAF           |
| VLAN Isolation          | FortiGate L3 Segmentation|
| Central Logging         | Graylog / Sentinel       |
| HA & Redundancy         | FortiGate Active-Passive |
| Config Backup           | Git Repo & NAS Share     |

---

## 📎 Conclusion

Hybrid University maintains a secure and segmented network architecture using Fortinet’s enterprise firewall and VPN solutions. By integrating perimeter defense, VLAN isolation, and encrypted remote access, we provide strong protection against internal and external threats while enabling flexibility for remote teaching and administration.
