# 🗃️ Internal Core Applications – Hybrid University

This document outlines the core internal applications and web services used across Hybrid University. These tools support day-to-day operations for faculty, staff, and students—focusing on learning, collaboration, development, and communication.

---

## 🔧 Core Web Application Stack

| Category             | Tool                                         | Hosting/Stack                         |
|----------------------|----------------------------------------------|----------------------------------------|
| **LMS**              | Moodle 4.3                                   | Self-hosted on AWS EC2, Ubuntu 22.04   |
| **File Collaboration**| SharePoint Online (staff) + OneDrive (students) | Microsoft 365 Cloud                   |
| **Intranet Wiki**    | BookStack                                     | Self-hosted on internal VM or EC2      |
| **Video Conferencing**| Zoom Business (EDU Plan)                     | Zoom Cloud                             |
| **Webmail**          | Microsoft Exchange Online                     | Microsoft 365                          |
| **Internal Git**     | Gitea (self-hosted) or GitHub EDU Org         | Docker-based or GitHub-managed         |

---

## 🧰 Application Summaries

### 🎓 Moodle LMS (Learning Management System)
- Hosted on AWS EC2 (t3.medium), using PHP, MariaDB, and NGINX stack.
- Integrated with Active Directory for authentication.
- Courses, quizzes, assignments, and attendance tracking.
- Backed up daily with `rclone + AWS S3`.

### 📁 SharePoint & OneDrive
- SharePoint: Used by departments and staff for document collaboration and internal wikis.
- OneDrive: Student file storage, linked to Microsoft EDU accounts.
- Integration with Microsoft Teams and Exchange Online.

### 📚 BookStack (Intranet Wiki)
- Markdown-based internal wiki for IT documentation, SOPs, and internal guides.
- Hosted on Proxmox VM or EC2 instance.
- Uses MySQL and Laravel backend; secure with HTTPS and LDAP login.

### 🎥 Zoom Business (EDU Plan)
- Used for all virtual classrooms and staff meetings.
- SSO integration with Microsoft 365.
- Licensed for up to 300 participants per session.
- Recordings stored locally or on Zoom Cloud (integrated with Moodle when needed).

### ✉️ Microsoft Exchange Online
- Cloud-hosted via Microsoft 365.
- Used for university-wide communication.
- Integrated with M365 Groups, Teams, and SharePoint permissions.
- DLP & email protection policies enforced via Microsoft Purview.

### 💻 Internal Git – Gitea or GitHub EDU
- **Gitea**: Lightweight self-hosted Git solution, used internally for version control of scripts, codebases, and web portals.
  - Hosted on Docker or EC2.
  - Integrated with LDAP login or OAuth.
- **GitHub EDU Org**: Optional use for collaborative research, student projects, and CI/CD with GitHub Actions.

---

## 🔐 Access & Security

| Tool        | Authentication         | Notes                                        |
|-------------|------------------------|----------------------------------------------|
| Moodle      | Active Directory (LDAP) | Azure AD Sync                                |
| SharePoint  | Microsoft 365 SSO       | Conditional Access policies applied          |
| BookStack   | LDAP or Microsoft OAuth | HTTPS enforced with Let’s Encrypt            |
| Gitea       | LDAP / Internal Auth    | Admin group mapped from AD                   |

---

## 🧪 Development & Testing Environment

- Development clones for Moodle, BookStack, and Gitea hosted on **Proxmox VMs**.
- CI/CD pipelines configured in GitHub for automated deployment/testing.
- Moodle and Gitea repositories maintained with branching for production, staging, and dev.

---

## 🗃️ Documentation & Wiki Practices

- BookStack organized by:
  - **IT SOPs**
  - **Security Guidelines**
  - **Teaching Tools & LMS Help**
  - **Staff Onboarding**
- Weekly backup of BookStack MySQL database to S3 via Rclone.

---

## 📌 Summary

Hybrid University’s internal web development stack blends cloud-native services (M365, Zoom, GitHub) with self-hosted platforms (Moodle, Gitea, BookStack) to offer a flexible, secure, and robust digital experience for students and staff. These tools are maintained with consistent monitoring, backups, and security practices to ensure high availability and compliance.
