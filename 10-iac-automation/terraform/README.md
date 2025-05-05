# ⚙️ Infrastructure as Code – Terraform @ Hybrid University

This section describes how Hybrid University uses Terraform to automate the provisioning and management of cloud infrastructure across AWS and Azure.

---

## 🌍 Overview

Terraform enables consistent, repeatable, and version-controlled infrastructure deployment. It eliminates manual cloud provisioning and ensures all environments (dev, staging, prod) are standardized.

---

## 📦 Toolchain

| Component          | Stack                                  | Purpose                              |
|-------------------|----------------------------------------|--------------------------------------|
| IaC Tool           | **Terraform 1.6**                      | Declarative cloud provisioning       |
| State Management   | **Terraform Cloud / AWS S3 + DynamoDB**| Store and lock infrastructure state  |
| Version Control    | **GitHub**                             | Source of truth for all `.tf` files  |
| Secrets Handling   | **Terraform Variables + AWS Secrets**  | Secure secrets and credentials       |

---

## ☁️ Supported Cloud Providers

- **AWS** – VPC, EC2, RDS, S3, IAM, Security Groups
- **Azure** – Resource Groups, VNets, VMs, Azure AD Join
- **(Optional)** GCP support in future via modules

---

## 📁 Project Structure

~~~
terraform/
├── aws/
│ ├── dev/
│ ├── staging/
│ └── prod/
├── azure/
│ ├── identity/
│ └── m365-integrations/
├── modules/
│ ├── ec2/
│ ├── vpc/
│ └── rds/
├── variables.tf
├── main.tf
└── outputs.tf

~~~

---

## 🚀 Deployment Workflow

1. **Git Commit**: `.tf` changes committed to GitHub
2. **Terraform Init**: Initialize working directory
3. **Terraform Plan**: Preview infrastructure changes
4. **Terraform Apply**: Deploy infrastructure

## 🧪 Example: Provision an AWS EC2 Instance (Beginner Guide)

### 1️⃣ Install Terraform

Download Terraform from: https://developer.hashicorp.com/terraform/downloads

Verify installation:

```bash
terraform -version
```

---

### 2️⃣ Create Files

Create a working folder (e.g. `terraform/aws/dev/`) and add the following:

#### **main.tf**

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c02fb55956c7d316"  # Ubuntu 22.04 LTS (Free Tier)
  instance_type = "t2.micro"

  tags = {
    Name = "HybridUniversity-Test"
  }
}

```

#### **variables.tf**
```hcl
variable "aws_region" {
  default = "us-east-1"
}
```

#### **outputs.tf**
```hcl
output "instance_id" {
  value = aws_instance.example.id
}
```

---

### 3️⃣ Initialize & Apply

```bash
terraform init            # Initialize provider plugins
terraform plan            # Show the execution plan
terraform apply           # Confirm and create resources
```

---

### 🧹 Clean Up

To destroy all created resources:

```bash
terraform destroy
```

---

## 🔄 Automation Workflow

1. Git Commit `.tf` changes to GitHub
2. PR Review & Merge
3. GitHub Actions triggers Terraform
4. Infrastructure gets provisioned

---

## 📊 Common Use Cases @ Hybrid University

| Use Case                | Description                          |
|-------------------------|--------------------------------------|
| AWS VPC Creation        | Standardized network setup           |
| RDS DB Setup            | PostgreSQL with parameter groups     |
| Azure VM Provisioning   | Domain-joined Windows VMs            |
| Intune Policy Templates | Azure-integrated device policies     |

---

## ✅ Summary Table

| Platform | Provider     | Key Resources         | State Management          | Automation |
|----------|--------------|-----------------------|---------------------------|------------|
| AWS      | `aws`        | EC2, RDS, VPC, IAM     | S3 + DynamoDB             | GitHub Actions |
| Azure    | `azurerm`    | VM, VNets, RG, AAD     | Terraform Cloud or Azure Blob | GitHub Actions |


## 🛡️ Security & Best Practices

Remote state locked via DynamoDB (AWS) or Terraform Cloud
Secrets never hardcoded – stored in environment variables or vaults
All changes go through pull requests + code review
Sensitive outputs (like passwords) are redacted in outputs

## 🔄 Automation Options

Tool	Role
GitHub Actions	CI/CD pipeline for infrastructure
Terraform Cloud	Collaborative runs and cost estimates
Pre-commit Hooks	Validate .tf syntax and formatting

## 📊 Example Use Cases

Use Case	Description
AWS VPC Creation	Standardized network setup
RDS DB Setup	PostgreSQL with parameter groups
Azure VM Provisioning	Domain-joined Windows VMs
Intune Policy Templates	Azure-integrated device policies

## 📌 Conclusion

Using Terraform, Hybrid University ensures scalable, secure, and consistent cloud infrastructure deployments across AWS and Azure. With IaC, teams reduce risk, enforce standards, and improve cloud governance.
