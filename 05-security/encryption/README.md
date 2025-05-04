# 🔐 Drive Encryption Strategy – Hybrid University

This document outlines the drive encryption practices employed at Hybrid University to ensure secure storage of data on all endpoints and servers. Encryption is enforced across all campuses and departments for both Windows and Linux systems.

---

## 🧰 Technologies Used

| Platform   | Encryption Tool         | Description                                                   |
|------------|--------------------------|---------------------------------------------------------------|
| Windows    | **BitLocker**            | Native full-disk encryption tool provided by Microsoft        |
| Linux      | **LUKS (dm-crypt)**      | Standard for disk encryption in Linux using cryptsetup        |

---

## 🎯 Encryption Objectives

- **Protect sensitive institutional data** at rest across all devices.
- Enforce **device-level compliance** with university data security policies.
- Mitigate **data breach risks** in case of device loss or theft.

---

## 🖥️ Windows Device Encryption – BitLocker

### 🔧 Configuration & Deployment

- Managed via **Microsoft Intune** (Endpoint Security > Disk Encryption Policy)
- TPM 2.0 is required and verified during setup.
- BitLocker is automatically enforced on system drives and fixed data drives.

### 💡 Policy Highlights

- **Encryption Algorithm**: XTS-AES 256-bit (default via Intune policy)
- **Recovery Key Backup**: Automatically stored in Azure AD for each device
- **Silent Enablement**: No user interaction required for eligible devices

### 📁 Targeted Devices

| Device Type         | Enforcement       | Status Monitoring            |
|---------------------|-------------------|-------------------------------|
| Laptops             | Mandatory         | Intune Compliance Dashboard   |
| Desktops            | Mandatory         | Azure AD BitLocker Reports    |
| Tablets/2-in-1      | Conditional       | Based on Intune Enrollment    |

---

## 🐧 Linux Device Encryption – LUKS

### 🔧 Configuration & Deployment

- Applied during OS installation or post-install via `cryptsetup`.
- Default algorithm: **AES-XTS 512 (256-bit key)** using LUKS2 format.
- Recovery keys stored securely in internal password vault (KeePassXC).

### 🛠️ Example Command

```bash
sudo cryptsetup luksFormat /dev/sdX
sudo cryptsetup open /dev/sdX encrypted_volume
mkfs.ext4 /dev/mapper/encrypted_volume

```

📅 Backup & Recovery Procedures

OS	Key Backup Strategy	Recovery Method
Windows	Azure AD automatic key upload	Admin retrieves from Intune/Azure AD
Linux	Manual vault (KeePassXC)	Encrypted volume unlocked manually

📊 Compliance Reporting

Windows Devices:
Compliant status verified in Microsoft Intune
Non-compliant devices trigger remediation workflows

Linux Devices:
Manual checklists logged in internal security audits
Future integration planned with OSQuery or Wazuh

🔐 Security Best Practices

TPM 2.0 enforced on all BitLocker-enabled devices
Startup PIN used for high-risk admin endpoints (optional)
No removable media without encryption (via Intune policy)
Secure password rotation and key vault backup practices

✅ Summary Table

Platform	Encryption Tool	Key Storage	Management Platform	Enforcement Status
Windows	BitLocker	Azure AD	Intune	Fully Enforced
Linux	LUKS (cryptsetup)	KeePassXC Vault	Manual / Planned via Ansible	Enforced at OS level

📌 Conclusion

The drive encryption policies at Hybrid University ensure that all institutional data is encrypted at rest, aligning with global best practices and compliance standards. By utilizing BitLocker for Windows and LUKS for Linux, the university achieves both operational flexibility and robust data protection across all systems.
