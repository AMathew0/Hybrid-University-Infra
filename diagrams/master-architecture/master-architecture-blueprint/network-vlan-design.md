# **Network & VLAN Design (README.md)**

---

### **Objective**

Design a scalable, secure, and segmented network architecture across **New Delhi, Bangalore, and Dubai**, accommodating hybrid services and on-prem/cloud connectivity.

---

## **1. High-Level Topology**

Each site includes:

* Core Switch + Access Switches
* Redundant Firewalls (FortiGate or pfSense)
* Dual WAN Links (Primary ISP + Failover)
* Layer 3 Segmentation
* Site-to-Site IPsec VPN between locations

---

## **2. Network Zones**

| VLAN Name   | VLAN ID | Subnet (Example) | Purpose                          |
| ----------- | ------- | ---------------- | -------------------------------- |
| **Mgmt**    | 10      | 10.10.10.0/24    | Switches, APs, Firewalls, etc.   |
| **Server**  | 20      | 10.10.20.0/24    | AD, DNS, Web, DB, etc.           |
| **Faculty** | 30      | 10.10.30.0/24    | Faculty desktops/laptops         |
| **Student** | 40      | 10.10.40.0/24    | BYOD or lab PCs                  |
| **Guest**   | 50      | 10.10.50.0/24    | Internet-only guest VLAN         |
| **IoT**     | 60      | 10.10.60.0/24    | CCTV, RFID, sensors              |
| **VoIP**    | 70      | 10.10.70.0/24    | Phones, conferencing devices     |
| **DMZ**     | 99      | 10.10.99.0/24    | Exposed services (Web, LMS, etc) |

Each site mirrors this structure but uses unique third octets:

* New Delhi: `10.10.x.x`
* Bangalore: `10.20.x.x`
* Dubai: `10.30.x.x`

---

## **3. Routing & Segmentation**

| Role                      | Technology                   |
| ------------------------- | ---------------------------- |
| **Inter-VLAN Routing**    | Core L3 Switch (or firewall) |
| **East-West Filtering**   | ACLs between VLANs           |
| **North-South Filtering** | Firewall zones (WAN/DMZ/LAN) |

Routing via:

* Layer 3 core switch (if available)
* Or firewall routing with zone-based policies

---

## **4. VPN Design**

| Type          | Purpose                 | Tool                  |
| ------------- | ----------------------- | --------------------- |
| **IPsec VPN** | Site-to-site tunnels    | FortiGate / pfSense   |
| **SSL VPN**   | Remote access for staff | FortiClient / OpenVPN |
| **MFA**       | Secure VPN login        | Azure AD MFA          |

---

## **5. High Availability & Failover**

| Component     | HA Method                 |
| ------------- | ------------------------- |
| Firewall      | Active-Passive Pairing    |
| Core Switches | Stack or VRRP             |
| WAN Links     | Dual ISP with failover    |
| DNS           | Internal + Cloud fallback |

---

## **6. Example IP Plan – New Delhi**

| VLAN    | Subnet        | DHCP Scope       | Notes                    |
| ------- | ------------- | ---------------- | ------------------------ |
| Mgmt    | 10.10.10.0/24 | Static only      | Firewalls, switches      |
| Server  | 10.10.20.0/24 | 10.10.20.100–200 | Domain Controllers, etc. |
| Faculty | 10.10.30.0/24 | 10.10.30.50–250  | End-user PCs             |
| Student | 10.10.40.0/24 | 10.10.40.50–250  | BYOD, labs               |
| Guest   | 10.10.50.0/24 | 10.10.50.10–240  | Internet only            |

---

## **7. Example ACL Rules**

| Source VLAN | Destination VLAN | Action | Description               |
| ----------- | ---------------- | ------ | ------------------------- |
| Faculty     | Server           | Allow  | For internal services     |
| Student     | Server           | Deny   | Block direct access       |
| IoT         | All              | Deny   | Isolate for security      |
| Mgmt        | All              | Allow  | Full access for IT admins |

---

## **8. Wireless SSIDs (Wi-Fi)**

| SSID Name  | VLAN | Auth Method     | Notes                        |
| ---------- | ---- | --------------- | ---------------------------- |
| HU-Staff   | 30   | WPA2-Enterprise | Mapped to faculty VLAN       |
| HU-Student | 40   | WPA2-Enterprise | Mapped to student VLAN       |
| HU-Guest   | 50   | Captive Portal  | Internet only, rate-limited  |
| HU-IoT     | 60   | MAC whitelist   | For sensors, projectors, etc |

---

## **9. Diagram (for later in `/diagrams/`)**

* **Layered Network Diagram:**

  * Core / Access switches
  * Firewall zones
  * VLAN layout
  * VPN tunnels

---
