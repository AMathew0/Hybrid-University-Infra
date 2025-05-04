# 🖥️ On-Premises Network Setup – Hybrid University

This section provides the on-premises network setup for Hybrid University, focusing on the Linux servers deployment. This setup ensures seamless integration with the university's cloud infrastructure while providing robust performance and security across various on-premises services.

## 🏢 On-Premises Linux Setup Overview

| **Server Type**    | **Location**                 | **Role/Service**                        | **Key Functions**                              |
|--------------------|-----------------------------|----------------------------------------|------------------------------------------------|
| **Linux Servers**   | India HQ, Dubai Branch, India Branch | Application Hosting, Web Servers, Monitoring | Host internal apps, databases, Zabbix, Graylog |

---

## 🔌 Linux Server Deployment

The Linux servers are deployed across three sites to host applications and provide monitoring services. Below is the breakdown of server deployment:

| **Server ID**     | **Server Type**    | **IP Address**      | **Services Hosted**                    | **Site**         |
|-------------------|--------------------|---------------------|----------------------------------------|------------------|
| **Linux-01**      | Ubuntu Server 20.04 | `192.168.10.10`     | Zabbix, File Sharing                  | India HQ         |
| **Linux-02**      | Ubuntu Server 20.04 | `192.168.40.10`     | Web Applications, Internal Apps       | Dubai Branch     |
| **Linux-03**      | Ubuntu Server 20.04 | `192.168.70.10`     | Zabbix Monitoring, Graylog            | India Branch     |

### Notes:
- **Linux Servers** are configured to handle application hosting, monitoring, and log management tasks. These servers are optimized to run high-performance services and applications with redundancy for high availability.
- Each site is equipped with local Linux servers to ensure minimal latency and maximum uptime for critical internal services.
- **Security**: Hardening techniques such as **firewall configurations**, **SSH key-based authentication**, and **regular patch management** are implemented.

---

## 🌐 Integration with Active Directory

Linux servers across all sites are integrated with the **Active Directory (AD)** domain to ensure centralized user management and security.

### Integration Details:
- **AD Domain**: `hybriduniversity.local`
- **Linux Authentication**: `SSSD` (System Security Services Daemon) and `Realmd` are used to join the Linux servers to the AD domain, enabling centralized login and access control.

---

## 🔄 Service Configuration

Each Linux server is configured to host key services that are crucial for Hybrid University's operations.

### **India HQ**:
- **Linux-01**:
  - **Zabbix Monitoring**: For performance and health monitoring of network devices, servers, and applications.
  - **File Sharing**: Provides file sharing and backups for staff and students.

### **Dubai Branch**:
- **Linux-02**:
  - **Web Applications**: Hosts internal apps and provides web services for the Dubai branch.
  - **Internal Apps**: Runs custom applications used by the faculty and staff.

### **India Branch**:
- **Linux-03**:
  - **Zabbix Monitoring**: Ensures real-time monitoring of all network infrastructure.
  - **Graylog**: For centralized log management and analysis.

---

## 🛡️ Security Measures

To maintain a secure and stable on-premises network environment, the following security measures are implemented:

### 1. **Firewalls**:
- Use of **UFW (Uncomplicated Firewall)** for filtering inbound and outbound traffic.
- Only necessary ports are opened, based on the specific service being hosted on each server.

### 2. **SSH Security**:
- **Key-Based Authentication** is enforced for SSH access, and **password-based authentication** is disabled.
- Only authorized personnel have access to SSH keys to prevent unauthorized access.

### 3. **System Hardening**:
- Regular **security patches** are applied to keep all Linux servers up to date.
- **Fail2Ban** is used to protect against brute-force attacks.
- **SELinux** is enabled for enhanced security policy enforcement.

---

## ✅ Summary Table

| **Server Name**    | **Site Location**  | **Key Services**                               | **IP Address**      | **Security Features**             |
|--------------------|-------------------|-----------------------------------------------|---------------------|-----------------------------------|
| **Linux-01**       | India HQ          | Zabbix Monitoring, File Sharing               | `192.168.10.10`     | SSH Key Auth, UFW, Fail2Ban       |
| **Linux-02**       | Dubai Branch      | Web Applications, Internal Apps               | `192.168.40.10`     | SSH Key Auth, UFW, SELinux        |
| **Linux-03**       | India Branch      | Zabbix Monitoring, Graylog                   | `192.168.70.10`     | SSH Key Auth, UFW, Fail2Ban       |

---

## 💼 Conclusion

The Linux server infrastructure at Hybrid University ensures the high availability of critical services such as monitoring, file sharing, and application hosting. These servers are tightly integrated with the university’s Active Directory, offering centralized management and robust security. The setup leverages Linux’s flexibility and security features to meet the university's demanding operational requirements.
