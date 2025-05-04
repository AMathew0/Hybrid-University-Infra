# 🌐 Network Security & Segmentation – Hybrid University

This document outlines the network segmentation strategy, VLAN architecture, IP addressing scheme, and backup policies implemented to ensure a secure and efficient network infrastructure across Hybrid University's campuses and departments.

---

## 🧱 Network Segmentation Overview

Network segmentation is employed to:

- **Enhance Security**: By isolating sensitive data and systems, lateral movement by potential attackers is limited.
- **Improve Performance**: Segmentation reduces broadcast domains, decreasing unnecessary traffic.
- **Simplify Management**: Different departments and functions can be managed independently.

---

## 🗂️ VLAN Architecture

### 🎯 VLAN Assignments

| **VLAN ID** | **Name**             | **Purpose**                              | **IP Subnet**       |
|-------------|----------------------|------------------------------------------|---------------------|
| 10          | Admin                | Administrative staff                     | 10.10.10.0/24       |
| 20          | Faculty              | Faculty members                          | 10.10.20.0/24       |
| 30          | Students             | Student access                           | 10.10.30.0/24       |
| 40          | Guest                | Guest Wi-Fi access                       | 10.10.40.0/24       |
| 50          | Servers              | Internal servers                         | 10.10.50.0/24       |
| 60          | Management           | Network management interfaces            | 10.10.60.0/24       |
| 70          | VoIP                 | Voice over IP devices                    | 10.10.70.0/24       |
| 80          | CCTV                 | Security cameras                         | 10.10.80.0/24       |
| 90          | Backup               | Backup systems and storage               | 10.10.90.0/24       |

### 🔐 VLAN Security Measures

- **Access Control Lists (ACLs)**: 
- **Private VLANs**:
- **802.1X Authentication**:

---

## 🧭 IP Addressing Scheme

### 📌 Subnet Details

| **VLAN**    | **Subnet**       | **Gateway**     | **DHCP Range**         |
|-------------|------------------|-----------------|------------------------|
| Admin       | 10.10.10.0/24    | 10.10.10.1      | 10.10.10.100 - .200    |
| Faculty     | 10.10.20.0/24    | 10.10.20.1      | 10.10.20.100 - .200    |
| Students    | 10.10.30.0/24    | 10.10.30.1      | 10.10.30.100 - .250    |
| Guest       | 10.10.40.0/24    | 10.10.40.1      | 10.10.40.100 - .250    |
| Servers     | 10.10.50.0/24    | 10.10.50.1      | Static IPs Assigned    |
| Management  | 10.10.60.0/24    | 10.10.60.1      | Static IPs Assigned    |
| VoIP        | 10.10.70.0/24    | 10.10.70.1      | 10.10.70.100 - .200    |
| CCTV        | 10.10.80.0/24    | 10.10.80.1      | 10.10.80.100 - .200    |
| Backup      | 10.10.90.0/24    | 10.10.90.1      | Static IPs Assigned    |
---

## 🔄 Inter-VLAN Routing & Firewall Policies

- **Layer 3 Switches**: 
- **Firewall Rules**: 
- **Guest VLAN**:

---

## 💾 Backup Strategies

### 🗃️ Backup Types

- **Full Backup**: 
- **Incremental Backup**: 
- **Differential Backup**: 

### 📆 Backup Schedule

| **System**     | **Type**       | **Frequency**     | **Retention Period** |
|----------------|----------------|-------------------|----------------------|
| Servers        | Full           | Weekly            | 4 Weeks              |
| Servers        | Incremental    | Daily             | 7 Days               |
| Databases      | Full           | Daily             | 7 Days               |
| User Devices   | Differential   | Weekly            | 2 Weeks              |
| Critical Data  | Full           | Daily             | 30 Days              |

### 🔐 Backup Storage

- **On-Site**: 
- **Off-Site**: 
- **Access Controls**: 

---

## 📊 Monitoring & Management

- **Network Monitoring**: 
- **IP Address Management (IPAM)**: 
- **Regular Audits**: 

---
