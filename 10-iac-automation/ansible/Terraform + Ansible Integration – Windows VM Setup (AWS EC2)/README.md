# 🧩 Terraform + Ansible Integration – Windows VM Setup (AWS EC2)

This guide demonstrates how to use **Terraform** to provision a Windows Server instance on AWS, and then hand over to **Ansible** for configuration (e.g., domain join, software install, hardening).

---

## 📦 Tools Required

- **Terraform ≥ 1.6**
- **Ansible ≥ 8.5**
- `pywinrm` for Ansible
- AWS credentials configured via `~/.aws/credentials` or environment variables

---

## 🗂️ Directory Structure

```
10-iac-automation/
├── terraform/
│   └── windows-ec2/
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── terraform.tfvars
├── ansible/
│   ├── inventories/
│   │   └── windows_ec2.yml  # generated from output
│   ├── playbooks/
│   │   └── windows-setup.yml
│   └── vault/
│       └── windows-secrets.yml
```

---

## 🔧 Terraform Config to Create Windows EC2

```hcl
# main.tf
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "win_ad_client" {
  ami           = "ami-0dfcb1ef8550277af" # Windows Server 2022
  instance_type = "t3.medium"
  key_name      = "hybrid-key"
  subnet_id     = var.subnet_id
  security_groups = [var.security_group]

  tags = {
    Name = "win-ad-client"
  }

  user_data = <<EOF
<powershell>
winrm quickconfig -q
winrm set winrm/config/winrs @{MaxMemoryPerShellMB="1024"}
winrm set winrm/config @{MaxTimeoutms="1800000"}
winrm set winrm/config/service @{AllowUnencrypted="true"}
winrm set winrm/config/service/auth @{Basic="true"}
Enable-PSRemoting -Force
</powershell>
EOF
}

output "windows_ec2_ip" {
  value = aws_instance.win_ad_client.public_ip
}
```

---

## ⚙️ Terraform Variables

```hcl
# variables.tf
variable "subnet_id" {}
variable "security_group" {}
```

---

## 🌍 Example Terraform Output Script (for Ansible)

```bash
# Get IP and create Ansible inventory
terraform output -raw windows_ec2_ip > ip.txt

cat <<EOF > ../ansible/inventories/windows_ec2.yml
windows:
  hosts:
    win-ec2:
      ansible_host: $(cat ip.txt)
      ansible_user: Administrator
      ansible_password: "<paste your password here>"
      ansible_connection: winrm
      ansible_winrm_transport: basic
      ansible_winrm_server_cert_validation: ignore
EOF
```

> ⚠️ Replace `<paste your password here>` with the EC2 admin password you retrieve from the EC2 instance metadata or use AWS SSM.

---

## ▶️ Run Terraform and Then Ansible

```bash
cd 10-iac-automation/terraform/windows-ec2
terraform init
terraform apply -auto-approve
```

Then:

```bash
cd ../ansible
ansible-playbook -i inventories/windows_ec2.yml playbooks/windows-setup.yml --ask-vault-pass
```

---

## 🔁 Workflow Summary

1. **Terraform** provisions Windows EC2 with WinRM enabled.
2. Terraform outputs IP.
3. Ansible inventory is dynamically generated.
4. **Ansible** configures system:
   - Joins to AD domain (on-prem or hybrid Azure AD)
   - Installs apps (7-Zip, antivirus, etc.)
   - Configures firewall, user accounts, etc.

---

## 🔐 Best Practices

| Area                | Best Practice                              |
|---------------------|---------------------------------------------|
| Credentials         | Store in `Ansible Vault`                   |
| SSH Key Pair        | Use secure `.pem` keys                     |
| WinRM Security      | Use SSL in production (self-signed OK)     |
| Inventory Mgmt      | Automate inventory output from Terraform   |
| Idempotency         | Ensure Ansible playbooks are idempotent    |

---

## 🚀 Next Steps

- Add **EC2 tags** and group naming conventions
- Use **Terraform remote state** for shared infra
- Integrate **SSM Session Manager** for remote Windows access
- Use **Packer** to pre-bake WinRM-enabled AMIs

---

## 📚 References

- [Terraform AWS Provider Docs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Ansible Windows Guide](https://docs.ansible.com/ansible/latest/user_guide/windows.html)
- [WinRM Setup for Ansible](https://docs.ansible.com/ansible/latest/user_guide/windows_winrm.html)
