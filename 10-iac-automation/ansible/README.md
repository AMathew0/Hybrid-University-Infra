# 🧩 Configuration Management – Ansible @ Hybrid University

Ansible is used to automate configuration management, software installation, security hardening, and post-deployment tasks across Linux and Windows servers at Hybrid University. It ensures consistency, speed, and compliance for multi-environment management.

---

## 📦 Toolchain Overview

| Component             | Stack / Version       | Purpose                              |
|----------------------|------------------------|--------------------------------------|
| CM Tool              | **Ansible 8.5**        | Agentless automation and config mgmt |
| OS Support           | Ubuntu 22.04+, CentOS 8+, Windows 10/11 |
| Inventory Mgmt       | YAML (static) + dynamic inventory scripts |
| Secrets Management   | **Ansible Vault**      | Encrypt credentials and secrets      |
| DevOps Integration   | **GitHub Actions**     | Trigger playbooks on push            |

---

## 🏛️ Use Cases @ Hybrid University

| Task                            | Description                                               |
|---------------------------------|-----------------------------------------------------------|
| Server hardening                | CIS benchmark configurations for Linux and Windows        |
| App deployment                  | Automate Moodle, Zabbix, Gitea, and BookStack setup       |
| User/group creation             | Bulk provisioning of students/dev users                   |
| Patch management                | Regular security updates and reboots                      |
| Post-deployment config          | VPC or EC2-specific provisioning post-Terraform           |

---

## 📁 Directory Structure

```
ansible/
├── inventories/
│   ├── dev/
│   │   └── hosts.yml
│   ├── staging/
│   └── prod/
├── playbooks/
│   ├── setup-moodle.yml
│   ├── harden-linux.yml
│   └── join-domain-windows.yml
├── group_vars/
│   └── all.yml
├── roles/
│   ├── common/
│   ├── nginx/
│   └── docker/
├── ansible.cfg
├── requirements.yml
└── vault/
    └── secrets.yml (encrypted)
```

---

## 🧪 Quick Start (For New Users)

### 1️⃣ Install Ansible (Ubuntu Example)

```bash
sudo apt update
sudo apt install -y ansible
ansible --version
```

---

### 2️⃣ Define Inventory

```yaml
# inventories/dev/hosts.yml
all:
  hosts:
    dev-server-1:
      ansible_host: 192.168.1.10
      ansible_user: ubuntu
  children:
    web:
      hosts:
        dev-server-1:
```

---

### 3️⃣ Create a Simple Playbook

```yaml
# playbooks/install-nginx.yml
- name: Install Nginx on Ubuntu
  hosts: web
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
```

---

### 4️⃣ Run the Playbook

```bash
ansible-playbook -i inventories/dev/hosts.yml playbooks/install-nginx.yml
```

---

## 🔐 Ansible Vault (Secrets Encryption)

```bash
ansible-vault create vault/secrets.yml
# Add variables like DB passwords or SSH keys
```

To use in playbooks:

```yaml
vars_files:
  - vault/secrets.yml
```

To edit:

```bash
ansible-vault edit vault/secrets.yml
```

---

## 🤖 Automation with GitHub Actions

Example `.github/workflows/deploy.yml`:

```yaml
name: Deploy Ansible Playbook

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install Ansible
        run: sudo apt install -y ansible
      - name: Run Playbook
        run: ansible-playbook -i inventories/prod/hosts.yml playbooks/setup-moodle.yml
```

---

## 🔄 Integration with Terraform

Use Terraform to provision infrastructure (e.g., EC2) → Use Ansible for post-deployment (install packages, configure firewall, domain join, etc.)

---

## 📊 Summary Table

| Feature           | Status                        |
|------------------|-------------------------------|
| Agentless        | ✅ Yes                         |
| Linux Support    | ✅ Ubuntu, CentOS              |
| Windows Support  | ✅ WinRM-based                 |
| Secrets Mgmt     | ✅ Via Ansible Vault           |
| GitOps Ready     | ✅ GitHub Actions / CLI        |
| Scalable         | ✅ Multi-node inventories       |

---

## 📌 Conclusion

Ansible is the backbone of configuration management at Hybrid University. It provides a consistent way to manage infrastructure and applications across diverse environments using declarative YAML playbooks and roles.

With Git-based version control and automation pipelines, it aligns with modern DevOps practices and simplifies both day-to-day operations and larger infrastructure rollouts.

