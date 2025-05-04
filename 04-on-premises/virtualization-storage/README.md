# 🖥️ On-Premises Virtualization & Storage Setup – Hybrid University

This section details the on-premises virtualization and storage setup at Hybrid University. The setup ensures that the university's virtual infrastructure is scalable, highly available, and optimized for performance. It includes virtualization technologies for hosting various services and centralized storage solutions for data management.

## 🏢 Virtualization & Storage Overview

| **Technology**     | **Location**         | **Role/Service**                         | **Key Functions**                               |
|--------------------|----------------------|------------------------------------------|-------------------------------------------------|
| **Virtualization** | India HQ, Dubai Branch, India Branch | Virtual Machine Hosting, Resource Allocation | Host virtual machines (VMs) for various services, optimize resource usage |
| **Storage**        | India HQ, Dubai Branch, India Branch | Centralized Storage, Backup Solutions     | Provide shared storage, backup, and high availability for critical data |

---

## 🔌 Virtualization Infrastructure

Virtualization is deployed across three sites, providing scalable and efficient use of hardware resources for hosting various virtual machines (VMs). The following outlines the virtualization infrastructure:

| **VM Host ID**    | **Hypervisor**      | **CPU**                | **RAM**               | **Storage**           | **Site**             |
|-------------------|---------------------|------------------------|-----------------------|-----------------------|----------------------|
| **VM-Host-01**    | VMware ESXi 7.0     | 16 vCPUs               | 64 GB                 | 2 TB SAN Storage      | India HQ             |
| **VM-Host-02**    | VMware ESXi 7.0     | 12 vCPUs               | 48 GB                 | 1 TB NAS Storage      | Dubai Branch         |
| **VM-Host-03**    | VMware ESXi 7.0     | 16 vCPUs               | 64 GB                 | 2 TB SAN Storage      | India Branch         |

### Notes:
- **VM Hosts** are configured with VMware ESXi as the hypervisor for running various virtual machines that host internal applications, databases, and other services.
- **VMs** are dynamically allocated based on workload requirements, ensuring optimal resource usage across the virtualized infrastructure.
- **Storage** for VMs is provisioned using high-performance SAN and NAS solutions, ensuring redundancy and reliability.

---

## 💾 Storage Infrastructure

The storage infrastructure at Hybrid University is designed to ensure high availability, reliability, and scalability for critical data. Below is the breakdown of the storage solutions:

| **Storage Solution**   | **Type**            | **Capacity**         | **Location**         | **Services**                  |
|------------------------|---------------------|----------------------|----------------------|-------------------------------|
| **SAN Storage**        | Fibre Channel       | 4 TB                 | India HQ             | Centralized VM storage, backups |
| **NAS Storage**        | iSCSI               | 3 TB                 | Dubai Branch         | Shared storage for applications |
| **Cloud Storage**      | AWS S3              | 10 TB                | All Sites            | Backup, data archival          |

### Key Points:
- **SAN Storage** provides centralized and high-speed storage for VMs and mission-critical applications. The storage solution is based on Fibre Channel technology to ensure low latency and high throughput.
- **NAS Storage** is used for shared storage solutions across branches, supporting file sharing and collaboration.
- **Cloud Storage** is integrated with the university’s cloud infrastructure for off-site backup and data archival, leveraging AWS S3 storage for scalability and durability.

---

## 🛠️ Backup & Disaster Recovery

A comprehensive backup and disaster recovery plan is in place to ensure the integrity and availability of the university's data.

### Backup Solutions:
- **VM Snapshots**: Regular snapshots of VMs are taken to ensure minimal downtime during failures or maintenance. Snapshots are taken at the following intervals:
  - **Daily** for critical VMs.
  - **Weekly** for non-critical VMs.
- **Data Backups**: Critical data on NAS and SAN storage is backed up daily to AWS S3 to ensure off-site storage for disaster recovery. Backup frequency is as follows:
  - **Full Backup**: Once a month (all data).
  - **Incremental Backup**: Daily (changed data).
  - **Differential Backup**: Weekly (data changes since the last full backup).

### Disaster Recovery Plan:
- **Failover Sites**: The virtualization infrastructure is designed with high availability in mind, ensuring failover capabilities across sites.
- **Data Replication**: Critical data on the SAN and NAS systems is replicated between sites to provide redundancy in case of site failure.

---

## 🌐 Network Segmentation & VLAN Configuration

To ensure security, efficiency, and optimal network performance, Hybrid University utilizes network segmentation and VLANs. Below is an overview of the network segmentation and VLANs deployed across the sites:

### VLANs Configuration:
| **VLAN ID** | **VLAN Name**       | **Subnet**          | **Purpose**                               | **Devices**                     |
|-------------|---------------------|---------------------|-------------------------------------------|---------------------------------|
| **10**      | **Management VLAN**  | `192.168.10.0/24`   | Network Management, Admin Access         | VM Hosts, Network Devices       |
| **20**      | **Application VLAN** | `192.168.20.0/24`   | Application Servers                      | Web, Database Servers           |
| **30**      | **Storage VLAN**     | `192.168.30.0/24`   | Shared Storage, Backup Servers           | NAS, SAN Storage                |
| **40**      | **User VLAN**        | `192.168.40.0/24`   | User Workstations, Faculty, Staff        | PCs, Laptops                    |
| **50**      | **Guest VLAN**       | `192.168.50.0/24`   | Guest Access, Public Internet            | Wireless Access Points (WAPs)   |

### Network Segmentation:
- **Security**: Each VLAN is isolated using **firewall rules** and **ACLs** to restrict unauthorized access between different network segments.
- **Performance**: VLANs reduce broadcast traffic and improve network efficiency by grouping devices based on their functions (e.g., User, Storage, Application).
- **Access Control**: VLANs are enforced with role-based access controls (RBAC), ensuring that only authorized users can access sensitive services or storage resources.

### IP Addressing Scheme:
- **Static IPs** are assigned to network devices such as **VM Hosts**, **NAS**, **SAN**, and **switches** for reliable access and management.
- **Dynamic IPs** are assigned to **workstations** and **laptops** using DHCP within the appropriate VLAN subnets.

---

## 🛡️ Security Measures

To ensure the security of virtualized infrastructure and storage, the following measures are in place:

### 1. **Encryption**:
- **Storage Encryption**: All data stored on SAN and NAS devices is encrypted to prevent unauthorized access.
- **VM Encryption**: Virtual machines are encrypted using built-in encryption features provided by VMware.

### 2. **Access Control**:
- **Role-Based Access Control (RBAC)** is implemented to restrict access to virtualized infrastructure and storage based on user roles.
- **Network Segmentation**: Virtualized environments are segmented into secure networks (VLANs) to prevent unauthorized access between different services.

### 3. **Monitoring & Alerts**:
- **Zabbix** is used to monitor the health and performance of virtualized infrastructure, ensuring proactive management of resources.
- **Graylog** is integrated to monitor logs from storage and virtualization systems for any suspicious activities or failures.

---

## ✅ Summary Table

| **Host Name**       | **Hypervisor**    | **Storage Type**  | **Location**         | **Key Services**             | **Security Features**      |
|---------------------|-------------------|-------------------|----------------------|-----------------------------|----------------------------|
| **VM-Host-01**      | VMware ESXi 7.0   | SAN Storage       | India HQ             | VM Hosting, Application Hosting | Encryption, RBAC           |
| **VM-Host-02**      | VMware ESXi 7.0   | NAS Storage       | Dubai Branch         | VM Hosting, Internal Services  | Encryption, RBAC           |
| **VM-Host-03**      | VMware ESXi 7.0   | SAN Storage       | India Branch         | VM Hosting, Application Hosting | Encryption, RBAC           |
| **SAN Storage**     | Fibre Channel     | 4 TB              | India HQ             | VM Storage, Backup            | Encryption                 |
| **NAS Storage**     | iSCSI             | 3 TB              | Dubai Branch         | Shared Storage                | Encryption                 |
| **Cloud Storage**   | AWS S3            | 10 TB             | All Sites            | Backup, Archival              | Encryption                 |

---

## 💼 Conclusion

The virtualization and storage infrastructure at Hybrid University provides a robust, scalable, and secure foundation for hosting critical services and applications. By leveraging VMware for virtualization and integrating high-performance storage solutions, the university ensures high availability and reliability across its on-premises services. The integration with cloud storage also enhances the backup and disaster recovery capabilities, offering a comprehensive strategy for data protection and business continuity. Additionally, network segmentation using VLANs ensures that the virtualized infrastructure remains secure and efficient.
