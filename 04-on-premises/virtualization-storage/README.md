# 🖥️ On-Premises Virtualization and Storage Setup – Hybrid University

This section outlines the virtualization and storage infrastructure for Hybrid University. The setup focuses on using **Proxmox VE 8.1** as the preferred hypervisor over ESXi for cost-effectiveness, along with **TrueNAS CORE** for network-attached storage (NAS), including NFS/SMB shares and ZFS replication. The solution also includes network segmentation, VLAN configurations, IP addressing strategies, and backup management.

---

## 🔧 Virtualization

The virtualization infrastructure is deployed using **Proxmox VE 8.1**. This solution provides a powerful and flexible hypervisor for managing virtual machines (VMs) and containers. Proxmox VE is chosen over ESXi due to its cost-effectiveness and feature set suitable for Hybrid University's needs.

### Key Features:
- **Hypervisor**: Proxmox VE 8.1
- **Virtual Machines**: Virtualize critical workloads, such as file servers, application servers, and database servers.
- **LXC Containers**: Lightweight containerization for less resource-intensive applications.
- **High Availability**: Clustering and automatic failover to ensure uninterrupted services.

---

## 🗄️ Storage – TrueNAS CORE

The storage infrastructure is based on **TrueNAS CORE**, which provides reliable and scalable storage for both network file shares (NFS/SMB) and data replication using ZFS.

### Storage Features:
- **TrueNAS CORE**:
  - **NFS/SMB Shares**: Shared network storage accessible across the university's various departments and locations.
  - **ZFS Replication**: For high availability and disaster recovery, TrueNAS uses **ZFS replication** to ensure data consistency and backup at multiple sites.
  - **Storage Pools**: ZFS pools provide efficient storage management with built-in data integrity checks.
  
### Storage Layout:
- **IP Addressing**: Each storage node will have a static IP within the private network to facilitate easy access and management.
  - **Example**: `192.168.10.20` for India HQ, `192.168.40.20` for Dubai Branch.
  
---

## 🌐 Network Segmentation and VLANs

To ensure security and optimized performance, **network segmentation** is implemented using VLANs. This structure isolates various network traffic types, reducing congestion and improving security.

### VLAN Configuration:
- **VLAN 10 - Management Network**: For administrative access to servers and storage.
- **VLAN 20 - Virtualization Network**: Dedicated VLAN for Proxmox and virtual machine traffic.
- **VLAN 30 - Storage Network**: Isolated network for TrueNAS storage replication and file sharing.
- **VLAN 40 - Backup Network**: Dedicated VLAN for backup traffic to ensure minimal impact on other services.

### IP Addressing:
- **Proxmox VE**: Each Proxmox node will be assigned a static IP within the **Virtualization Network (VLAN 20)**.
  - Example: `192.168.20.10` for Proxmox node at India HQ.
  
- **TrueNAS**: Storage nodes will be placed within the **Storage Network (VLAN 30)** with static IPs:
  - Example: `192.168.30.10` for India HQ storage node.

---

## 💾 Backup Strategy

A robust **backup strategy** is essential for ensuring data integrity and recovery in case of failure.

### Backup Strategy Components:
1. **Backup Duration**: 
   - **Daily Backups**: Full backups of critical systems, databases, and virtual machines.
   - **Weekly Full Backups**: Full backup of TrueNAS storage volumes.
   - **Monthly Archival Backups**: Offline backups stored off-site or in cloud storage.

2. **Backup Tools**:
   - **TrueNAS CORE Replication**: ZFS snapshots and replication ensure high availability and disaster recovery.
   - **Proxmox VE Backup**: Utilizes Proxmox’s native backup solution for VM-level backups.

3. **Backup Retention**:
   - Retain daily backups for 7 days, weekly backups for 4 weeks, and monthly backups for 6 months.

---

## 🔄 Patch Management

To maintain the integrity and security of the infrastructure, regular patching of both **Proxmox VE** and **TrueNAS CORE** systems is carried out.

### Patch Management Strategy:
- **Windows Server Update Services (WSUS)**: Used for managing and deploying Windows patches across the network.
- **PDQ Deploy (Free)**: Automates patch deployment on non-Windows systems and helps ensure consistency in updates.

---

## ✅ Summary Table

| **Component**      | **Solution**            | **Key Features**                        | **IP Addressing**          |
|--------------------|-------------------------|----------------------------------------|----------------------------|
| **Hypervisor**     | Proxmox VE 8.1          | VM and container management, High Availability | Static IPs on VLAN 20 |
| **Storage**        | TrueNAS CORE            | NFS/SMB Shares, ZFS Replication        | Static IPs on VLAN 30 |
| **Backup**         | ZFS Replication, Proxmox Backup | Daily and weekly backups, Data replication | Retention: Daily 7 days, Weekly 4 weeks |
| **Patch Management** | WSUS + PDQ Deploy Free | Windows patch management, Automated updates | Managed across all servers |

---

## 💼 Conclusion

The virtualization and storage infrastructure for Hybrid University ensures high availability, data integrity, and secure management of virtualized workloads. By utilizing **Proxmox VE 8.1** for virtualization and **TrueNAS CORE** for storage, the setup offers cost-effective solutions without compromising performance. The implementation of VLANs and a solid backup strategy further secures the university's critical infrastructure. Patch management tools ensure the environment stays secure and up-to-date with minimal manual intervention.
