# 🪟 Ansible for Windows Device Provisioning – Hybrid University

This guide outlines how Ansible is used to configure and join Windows devices to the domain (either on-prem AD or Azure AD Hybrid), install common software, apply hardening, and enforce baseline configurations.

---

## 🖥️ Prerequisites

1. **Windows Devices**:
   - WinRM (5986) enabled and configured
   - Admin credentials available
   - TLS certificate or self-signed accepted

2. **Control Node** (Ansible installed):
   - Ansible ≥ 8.5
   - `pywinrm` Python module

3. **Credentials Vaulted** using Ansible Vault.

---

## 🔧 Install WinRM Libraries (on Ansible Control Node)

```bash
pip install "pywinrm[credssp]"
```

---

## 🗂️ Inventory File (Static Example)

```yaml
# inventories/windows/hosts.yml
windows:
  hosts:
    win-client-1:
      ansible_host: 10.10.0.101
      ansible_user: administrator@hybriduniversity.local
      ansible_password: "{{ vault_win_admin_password }}"
      ansible_connection: winrm
      ansible_winrm_transport: basic
      ansible_winrm_server_cert_validation: ignore
```

---

## 🔐 Ansible Vault Example

```bash
ansible-vault create vault/windows-secrets.yml
```

```yaml
# vault/windows-secrets.yml
vault_win_admin_password: 'YourSecurePassword'
domain_admin_user: 'admin@hybriduniversity.local'
domain_admin_pass: 'AnotherSecurePassword'
```

---

## 🧰 Sample Playbook – Join AD, Install Software, Set Firewall

```yaml
# playbooks/windows-setup.yml
- name: Windows Provisioning
  hosts: windows
  vars_files:
    - ../vault/windows-secrets.yml
  tasks:

    - name: Join to AD domain
      win_domain_membership:
        dns_domain_name: hybriduniversity.local
        domain_admin_user: "{{ domain_admin_user }}"
        domain_admin_password: "{{ domain_admin_pass }}"
        state: domain
      register: domain_join_result
      when: "'Domain' not in ansible_domain"

    - name: Reboot if domain join required
      win_reboot:
      when: domain_join_result.reboot_required

    - name: Install 7-Zip
      win_chocolatey:
        name: 7zip
        state: present

    - name: Set firewall rule - allow RDP
      win_firewall_rule:
        name: "Allow RDP"
        localport: 3389
        action: allow
        direction: in
        protocol: TCP
        state: present
```

---

## 🏃 Run the Playbook

```bash
ansible-playbook -i inventories/windows/hosts.yml playbooks/windows-setup.yml --ask-vault-pass
```

---

## ✅ Outcome

- Windows device joined to domain
- Common software (e.g. 7-Zip) installed via Chocolatey
- Firewall rules set as per policy
- Reboot if required

---

## 📌 Tips for Hybrid University

| Use Case                | Tool / Role                     |
|-------------------------|---------------------------------|
| Domain Join             | `win_domain_membership`         |
| Software Deployment     | `win_chocolatey`, MSI installs  |
| Firewall & Policies     | `win_firewall_rule`, `win_regedit` |
| Security Hardening      | Custom PowerShell via `win_shell` |
| User Creation           | `win_user`, `win_group`         |
| Shared Drive Mapping    | `win_command` with `net use`    |

---

## 🔄 Integration Ideas

- Trigger Ansible playbook post-provision from **Terraform**.
- Combine with **Intune policies** for additional controls.
- Use tags to separate student, faculty, or lab configurations.

---

## 📚 References

- [Ansible Windows Modules](https://docs.ansible.com/ansible/latest/collections/ansible/windows/)
- [Chocolatey for Ansible](https://docs.ansible.com/ansible/latest/collections/chocolatey/chocolatey/win_chocolatey_module.html)
