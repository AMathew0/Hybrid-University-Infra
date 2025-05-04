# 🎓 Learning Management System (LMS) – Hybrid University

This document outlines the architecture, deployment, and maintenance strategy for the Learning Management System (LMS) at Hybrid University. The platform empowers online and hybrid learning for students and educators using Moodle 4.3.

---

## 🌐 Platform Overview

| Component        | Technology Stack              |
|------------------|-------------------------------|
| LMS Platform     | **Moodle 4.3** (Open Source)  |
| Hosting          | **AWS EC2** (t3.medium)       |
| Web Server       | **NGINX**                     |
| Backend          | **PHP 8.1**, **MariaDB 10.6** |
| OS               | Ubuntu 22.04 LTS              |
| Storage          | EBS Volume + S3 Offload Plugin |
| Backup           | Rclone + AWS S3, daily cron   |

---

## 📦 Key Features

- Modular course creation, quizzes, and assignments
- User management integrated with **LDAP/Active Directory**
- Self-enrollment and course completion tracking
- Plugin support: BigBlueButton, Attendance, Custom Themes
- Mobile responsive via Moodle App integration

---

## 🛠️ Deployment Architecture

~~~
+----------------+ +----------------+
| User Devices | <---> | AWS CloudFront| (optional CDN)
+----------------+ +--------+-------+
|
v
+--------------+
| NGINX (HTTPS)|
+-------+------+
|
+-----------------------------+
| PHP-FPM + Moodle Core |
+-----------------------------+
|
+-----------------+
| MariaDB RDS |
+-----------------+
|
+----------------+
| AWS S3 Backup |
+----------------+

~~~

---

## 🔐 Security & Access

- HTTPS enforced using **Let’s Encrypt SSL**
- Fail2Ban enabled for SSH and Moodle login attempts
- Admin access limited to VPN-only IPs (via AWS SG)
- Database access via RDS security group and bastion host

---

## 👥 User Authentication

| Method          | Description                                       |
|-----------------|---------------------------------------------------|
| **LDAP (Primary)** | Integrated with on-premises AD & Azure AD sync |
| Manual          | Used for admin accounts or emergency access       |
| OAuth (Future)  | Planned integration with Microsoft SSO            |

---

## 📆 Backup & Disaster Recovery

| Component        | Frequency     | Backup Tool        | Storage Destination |
|------------------|---------------|--------------------|---------------------|
| Database (MariaDB)| Daily @ 2AM   | `mysqldump` via cron | AWS S3 (encrypted)  |
| Moodle Files      | Daily @ 3AM   | `rsync + rclone`     | AWS S3               |
| Full EC2 Snapshot | Weekly        | AWS Backup           | AWS EBS Snapshot     |

---

## 📈 Monitoring & Performance

- Zabbix Agent installed on EC2 instance for CPU, memory, and disk monitoring
- Application-level monitoring via Moodle’s built-in logs and logstore plugin
- Failures or errors trigger email alerts to LMS Admin group

---

## 📌 Maintenance & Updates

| Task                  | Frequency          |
|-----------------------|--------------------|
| Moodle Core Updates   | Monthly (manually tested) |
| Plugin Updates        | Bi-monthly         |
| System Patching       | Weekly (unattended-upgrades) |
| User Audit & Cleanup  | Quarterly          |

---

## 📊 LMS Usage Metrics (Sample)

| Metric                   | Monthly Avg     |
|--------------------------|-----------------|
| Active Users             | ~350            |
| Course Completions       | 120+            |
| Concurrent Sessions Peak | ~80             |
| Video Bandwidth Usage    | ~150 GB/month   |

---

## 🧩 Notable Plugins

- **Attendance**: Track student participation
- **BigBlueButton**: Video conferencing integration
- **Adaptable Theme**: Custom branding
- **Reengagement**: Automate reminder emails

---

## ✅ Summary

Hybrid University’s LMS is a scalable, self-hosted Moodle 4.3 deployment hosted on AWS infrastructure. It is secured with best practices, tightly integrated with the identity system, and built for growth in user base and functionality.
