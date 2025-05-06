
# 🧩 Step 7: Network Architecture Design

---

## 🗺️ Goals of Network Design

| Goal                           | Description                                                |
| ------------------------------ | ---------------------------------------------------------- |
| Segmentation                   | Separate networks for departments, guest, staff, IoT, etc. |
| Redundancy & Uptime            | Redundant core switches, dual uplinks, HA firewalls        |
| Secure Inter-Site Connectivity | VPN/IPSec/BGP tunnels between locations                    |
| Central Management             | VLANs managed centrally via core switches / firewalls      |
| Performance Optimization       | QoS for voice/video; bandwidth shaping                     |
| Scalability                    | Ready to add future campuses or cloud regions              |

---

## 🏫 Multi-Site Locations

| Site      | Type              | Description                                                |
| --------- | ----------------- | ---------------------------------------------------------- |
| New Delhi | HQ & Datacenter   | Primary infrastructure, on-prem servers, internet breakout |
| Bangalore | Satellite Campus  | Full connectivity, local compute & storage                 |
| Dubai     | Teaching Facility | Access via VPN + Cloud-only apps                           |

> **All sites are connected via secure IPSec VPN tunnels** and optionally BGP for dynamic routing.

---

## 🌐 VLAN Design Per Site

| VLAN ID | Name        | IP Range        | Purpose                         |
| ------- | ----------- | --------------- | ------------------------------- |
| 10      | MGMT        | 192.168.10.0/24 | Networking gear, firewalls, UPS |
| 20      | Staff       | 192.168.20.0/24 | Faculty & admin systems         |
| 30      | Students    | 192.168.30.0/24 | BYOD, lab desktops              |
| 40      | Guests      | 192.168.40.0/24 | Internet-only, isolated         |
| 50      | IoT Devices | 192.168.50.0/24 | CCTV, sensors, biometric        |
| 60      | Servers     | 192.168.60.0/24 | LMS, ERP, Git, Monitoring       |
| 70      | Backup NAS  | 192.168.70.0/24 | Isolated backup-only subnet     |

Each VLAN is **tagged** and managed via **Layer 3 core switches (e.g., Cisco SG500/SwitchStack)** or **FortiGate Firewall routing interfaces**.

---

## 🔄 Inter-Site Routing

| Method          | Technology Used               | Notes                              |
| --------------- | ----------------------------- | ---------------------------------- |
| Tunneling       | IPSec VPN over WAN links      | FortiGate IPsec site-to-site VPN   |
| Dynamic Routing | BGP between sites             | Redundant path selection, failover |
| Static Routing  | On smaller links or firewalls | Simpler but limited scalability    |

> Redundant VPN tunnels (active/passive) ensure **failover** between locations.

---

## 🔐 Firewall & Access Control Zones

| Zone           | Description                      | Example Rules                      |
| -------------- | -------------------------------- | ---------------------------------- |
| Internal (LAN) | Trusted VLANs                    | Allow full access                  |
| DMZ            | Exposed services (LMS, Git, VPN) | Public → LMS (HTTPS), SSH (admins) |
| Guest          | Isolated VLAN                    | Only outbound to internet          |
| IoT            | Isolated VLAN                    | Allow NTP/DNS, block internet      |
| Mgmt           | Admin-only VLAN                  | Allow RDP/SSH, block web traffic   |

Firewall is configured with **zone-based policies**, **IPS**, and **DoS protection**.

---

## 🛰️ Example FortiGate Interface Setup

```bash
Port1 → VLAN 10 (MGMT)
Port2 → VLAN 20 (Staff)
Port3 → VLAN 30 (Student)
Port4 → VLAN 60 (Servers)
Port5 → Internet (WAN)
VPN → IPSec to Bangalore / Dubai
```

---

## 📶 Wireless SSID Mapping to VLANs

| SSID Name          | VLAN    | Access Control                  |
| ------------------ | ------- | ------------------------------- |
| University-Staff   | VLAN 20 | WPA2-Enterprise via RADIUS (AD) |
| University-Student | VLAN 30 | WPA2-Enterprise (AD/Google)     |
| Guest-WiFi         | VLAN 40 | Captive portal + time limits    |
| IoT-Zone           | VLAN 50 | MAC-based filter, isolated      |

Wireless access via **UniFi 6 Pro APs**, managed via UniFi Controller (Cloud Key / self-hosted).

---

## 🧰 Sample Device Count Per Site

| Device Type        | Qty/Site | Notes                                       |
| ------------------ | -------- | ------------------------------------------- |
| FortiGate Firewall | 1–2 (HA) | 200D (HQ), 100D (Remote) with IPsec VPN     |
| Cisco L3 Switches  | 2–3      | SG500 core with trunk links                 |
| WiFi APs (UniFi 6) | 10–20    | Separate SSIDs/VLANs mapped                 |
| Servers (Proxmox)  | 2 + 1 DR | VMs for Moodle, Git, BookStack, ERP, Zabbix |
| Backup NAS         | 1–2      | Synology/TrueNAS, rsync enabled             |
| CCTV & Sensors     | 20+      | VLAN 50, PoE switches                       |

---

## 🧪 Sample Connection Flow: Remote Staff (Bangalore to New Delhi LMS)

```text
1. Staff logs in to laptop at Bangalore campus
2. Authenticated against Azure AD (cloud-first)
3. Staff opens Moodle via DNS (LMS.univ.edu)
4. DNS resolves to FortiGate public IP (New Delhi)
5. SSL traffic routed over site-to-site IPsec VPN
6. FortiGate DNAT → Internal LMS server on VLAN 60
```

---

## 🧯 High Availability & Failover Strategy

| Component       | Methodology                      |
| --------------- | -------------------------------- |
| Firewall HA     | FortiGate Active-Passive (FGCP)  |
| Core Switch HA  | Redundant stacking (Cisco SG)    |
| Proxmox Cluster | 2-node cluster + cold standby VM |
| DNS Failover    | Secondary DNS / Route53 failover |
| NAS Backup      | Rsync to off-site + AWS S3 Sync  |

---

## 🌍 Cloud-Hybrid Access Notes

* Azure AD is the central SSO point for all cloud-based services
* All cloud-bound traffic (O365, Zoom, GitHub, AWS) uses **split tunneling** for optimization
* Bandwidth management and QoS are applied at **FortiGate UTM level**

---
