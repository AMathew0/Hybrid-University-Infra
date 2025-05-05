# 🧩 Terraform + Ansible Integration – Linux VM Setup (AWS EC2)

This setup provisions a **Linux EC2 instance (Ubuntu 22.04)** via Terraform, then uses Ansible to configure it (installing agents, security tools, monitoring, etc.).

---

## 📦 Tools Required

- Terraform ≥ 1.6
- Ansible ≥ 8.5
- AWS CLI with configured credentials
- SSH key pair (`.pem` file) for access

---

## 🗂️ Directory Structure

```
10-iac-automation/
├── terraform/
│   └── linux-ec2/
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── terraform.tfvars
├── ansible/
│   ├── inventories/
│   │   └── linux_ec2.yml  # generated from Terraform output
│   ├── playbooks/
│   │   └── linux-setup.yml
│   └── vault/
│       └── linux-secrets.yml
```

---

## 🔧 Terraform – Create Linux EC2 Instance

```hcl
# main.tf
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "linux_node" {
  ami           = "ami-0fc5d935ebf8bc3bc" # Ubuntu Server 22.04 LTS (HVM), SSD
  instance_type = "t3.micro"
  key_name      = var.key_name
  subnet_id     = var.subnet_id
  security_groups = [var.security_group]

  tags = {
    Name = "linux-ansible-node"
  }
}

output "linux_ec2_ip" {
  value = aws_instance.linux_node.public_ip
}
```

---

## ⚙️ Terraform Variables

```hcl
# variables.tf
variable "key_name" {}
variable "subnet_id" {}
variable "security_group" {}
```

---

## 🌍 Generate Ansible Inventory

After `terraform apply`, run this shell script to create inventory:

```bash
# generate-linux-inventory.sh
IP=$(terraform output -raw linux_ec2_ip)

cat <<EOF > ../ansible/inventories/linux_ec2.yml
linux:
  hosts:
    ubuntu-node:
      ansible_host: $IP
      ansible_user: ubuntu
      ansible_ssh_private_key_file: "~/.ssh/hybrid-aws.pem"
      ansible_python_interpreter: /usr/bin/python3
EOF
```

---

## ▶️ Example Ansible Playbook

```yaml
# linux-setup.yml
- name: Configure Ubuntu EC2 node
  hosts: ubuntu-node
  become: true

  tasks:
    - name: Update APT cache
      apt:
        update_cache: yes

    - name: Install basic packages
      apt:
        name:
          - curl
          - git
          - ufw
          - fail2ban
        state: present

    - name: Enable UFW firewall
      ufw:
        state: enabled
        rule: allow
        port: ssh
        proto: tcp

    - name: Add monitoring agent (Zabbix or Prometheus node exporter)
      apt:
        name: prometheus-node-exporter
        state: present
```

---

## ▶️ Run Terraform and Ansible

```bash
cd 10-iac-automation/terraform/linux-ec2
terraform init
terraform apply -auto-approve
```

Then:

```bash
bash generate-linux-inventory.sh

cd ../ansible
ansible-playbook -i inventories/linux_ec2.yml playbooks/linux-setup.yml
```

---

## 🔐 Best Practices

| Area            | Best Practice                                |
|------------------|-----------------------------------------------|
| SSH Keys         | Use `ansible_ssh_private_key_file` securely  |
| Ansible Vault    | Encrypt secrets like domain creds            |
| Security Groups  | Restrict SSH access to specific IPs          |
| Monitoring       | Add Zabbix/Prometheus agents                 |
| Tags & Naming    | Use consistent AWS tags                      |

---

## 🚀 Extend This Setup

- Join Ubuntu to **FreeIPA** or **AD domain**
- Install **Docker**, configure firewall rules
- Add **Fail2Ban** or **CrowdSec** for intrusion prevention
- Bake custom AMIs with **Packer**

---

## 📚 References

- [Terraform AWS Provider Docs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Ansible for Ubuntu](https://docs.ansible.com/ansible/latest/user_guide/intro_getting_started.html)
