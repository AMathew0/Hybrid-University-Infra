# AWS Cloud Infrastructure – Hybrid University

## Purpose

This section outlines the detailed AWS infrastructure used in our Hybrid University environment. AWS services are integrated with on-premises infrastructure using secure, best-practice architecture models.
AWS is used for general hosting, academic web portals, storage, and serverless functions.

## Core Services Used

| Service          | Purpose |
|------------------|---------|
| VPC              | Custom network segmentation for university web and API systems |
| EC2 (Amazon Linux 2023) | Hosting public web applications |
| S3               | Backup and archival storage |
| Lambda           | Serverless grading & form processing |
| RDS (PostgreSQL 15) | University DB for scheduling & admin |
| Route 53         | DNS for custom university subdomains |
| CloudWatch       | Logs and monitoring |
| IAM              | Access controls and least privilege enforcement |

## Regions

- **ap-south-1 (Mumbai)** for latency to regional campuses
- Use of multi-AZ and S3 Cross-Region Replication for backup durability

## Notes

- EC2 instances hardened with CIS benchmarks
- Lifecycle policies applied on S3 for archiving
---

## 🌐 VPC Design

### VPC Layout

We use a **multi-tier VPC** architecture with separate subnets for **public**, **application**, and **database** tiers, and enforce network segmentation with security groups and NACLs.

#### Example VPC Setup

| Component              | CIDR Block       | Purpose                         |
|------------------------|------------------|---------------------------------|
| VPC                    | `10.10.0.0/16`   | Main VPC for the university |
| Public Subnet 1        | `10.10.1.0/24`   | Public-facing resources (Load Balancers, NAT Gateway) -  Public Subnet (for Bastion, NAT Gateway)   |
| Public Subnet 2        | `10.10.2.0/24`   | Secondary public subnet (Redundancy) - Private Subnet (for RDS, EC2)|
| App Subnet             | `10.10.3.0/24`   | Application servers (LMS, APIs) - Isolated Subnet (for Lambda, internal services)|
| DB Subnet              | `10.10.4.0/24`   | Database servers (RDS, backups) |

---

- **Routing**:
  - Public subnet routed to Internet Gateway.
  - Private subnet routed via NAT Gateway.
  - VPN routes to on-premises via Virtual Private Gateway.

---

## 🔐 Hybrid Connectivity

- **VPN Type**: Site-to-Site IPsec VPN
- **On-Prem Device**: FortiGate 200D with VPN enabled
- **AWS Device**: Virtual Private Gateway (VGW)
- **Routing Strategy**:
  - Static routes between on-prem network `192.168.0.0/16` and AWS `10.100.0.0/16`
- **Backup**: Optional SD-WAN via secondary cloud platform

---

## 🔒 Security & Firewall

### 🔹 Security Groups (Stateful)
- EC2: Allow SSH (port 22) from Bastion only
- RDS: Allow DB port (3306 for MySQL) from Private Subnet only

### 🔸 NACLs (Stateless)
- Public subnet: Inbound 22, 443; Outbound All
- Private subnet: Allow inbound from Public subnet and VPN

### 🔹 AWS WAF
- Web ACL attached to ALB serving university web app (Protect against SQLi, XSS)

### 🛡 AWS Shield Standard
- DDoS protection for ALB and CloudFront distributions

---

## 🔐 Remote Access – RDS via Bastion

- **Bastion Host**:
  - Deployed in Public Subnet
  - Hardened Amazon Linux 2 EC2 (t3.micro)
  - Security Group: SSH (port 22) from Admin IPs
  - Used to connect to private RDS using internal IP

- **Alternative**: AWS Systems Manager Session Manager (No open ports required)

---

## ☁️ Core Services

| Service | Use Case | Configuration |
|--------|----------|---------------|
| EC2 | Web/App servers, Bastion Host | Amazon Linux 2, Ubuntu 22.04, Windows Server 2019 – t3.medium, t3.large |
| RDS | Relational database | MySQL 8.0, db.t3.medium, Multi-AZ enabled |
| S3 | File storage, logs, backups | Lifecycle rules: Move to Glacier after 30 days, delete after 365 days |
| Lambda | Automation, lightweight APIs | Python 3.10 runtimes for serverless functions |
| IAM | Access management | Role-based access for dev, ops, backup, read-only |
| CloudWatch | Monitoring & alerts | Alarms for CPU, disk, memory, S3 object events |
| Route 53 | Internal/external DNS | Split-horizon DNS zones for hybrid access |
| KMS | Encryption | Default encryption for S3, EBS, and RDS with AWS-managed keys |

---

## 🛠 EC2 Configuration

- **OS Images**:
  - Ubuntu Server 22.04
  - Windows Server 2019 Datacenter
  - Amazon Linux 2
  - 
### EC2 Instance Types & Hardening

**EC2 Types:**
- **Web Servers:** `t3.medium` (2 vCPUs, 4GB RAM)
- **App Servers:** `t3.large` (2 vCPUs, 8GB RAM)
- **Database Servers:** `r5.xlarge` (4 vCPUs, 32GB RAM) for RDS instances
- **Backup Servers:** `t3.micro` for lightweight backup services

### EC2 Hardening (CIS Benchmarks)

All EC2 instances are **hardened** based on the **CIS AWS Foundations Benchmark** to ensure secure configurations. Key security measures include:
- **SSH Key Pair:** Disable password authentication for SSH, enforce key-based access.
- **Security Groups:** Only allow HTTP/HTTPS from the internet, RDP from specific IP ranges.
- **CloudWatch Logs:** Enable CloudWatch logs for all SSH sessions.
- **OS Hardening:** Disable unnecessary services, update systems regularly, and configure logging for audit purposes.

#### Example EC2 Security Group (Web Server):

```json
{
  "SecurityGroupId": "sg-xxxxxxxx",
  "GroupName": "web-server-sg",
  "IpPermissions": [
    {
      "IpProtocol": "tcp",
      "FromPort": 80,
      "ToPort": 80,
      "IpRanges": [{"CidrIp": "0.0.0.0/0"}]
    },
    {
      "IpProtocol": "tcp",
      "FromPort": 443,
      "ToPort": 443,
      "IpRanges": [{"CidrIp": "0.0.0.0/0"}]
    }
  ]
}

```

💾 RDS (Relational Database Service)

The university uses Amazon RDS for database management, with MySQL as the engine.

RDS Instance Setup
Component	Setting	Value
DB Engine	MySQL	Version 8.0
Instance Type	db.m5.large	2 vCPUs, 8GB RAM
Storage	General Purpose (SSD)	100GB
Multi-AZ	Enabled	For high availability and failover
Backup Retention	30 days	Automated backups enabled

RDS Security Group (Sample)

```json
{
  "SecurityGroupId": "sg-xxxxxxxx",
  "GroupName": "rds-db-sg",
  "IpPermissions": [
    {
      "IpProtocol": "tcp",
      "FromPort": 3306,
      "ToPort": 3306,
      "IpRanges": [{"CidrIp": "10.10.4.0/24"}]
    }
  ]
}
```

🧭 Route 53 - DNS Management

Route 53 is used for DNS management, including domain registration and hybrid DNS setup.

Example Route 53 DNS Setup
Record Type	Name	Value
A (Address)	www.university.edu	192.0.2.1 (Elastic Load Balancer)
CNAME	lms.university.edu	lms-app-12345.elb.amazonaws.com
MX (Mail Exchange)	university.edu	10 mail.university.edu (Mail Server)

👮 IAM Configuration - User Roles and Policies

IAM (Identity and Access Management) roles and policies are critical for controlling access within AWS. Specific roles are created for system admins, developers, and service accounts.

Example IAM Role for EC2 Instance:
Role Name: EC2InstanceRole

```json

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:DescribeInstances",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::univ-backups/*"
    }
  ]
}
```

IAM Role for Lambda Function

Role Name: LambdaBackupRole

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::univ-backups/*"
    },
    {
      "Effect": "Allow",
      "Action": "rds:DescribeDBInstances",
      "Resource": "*"
    }
  ]
}
```
📊 CloudWatch Monitoring & Alerts

CloudWatch is used for logging and monitoring EC2, RDS, Lambda, and other AWS services. Alarms are set for key metrics such as CPU utilization, storage, and backup success.

Example CloudWatch Alarm for EC2 CPU Utilization:

```json
{
  "AlarmName": "EC2-High-CPU",
  "AlarmDescription": "Alarm for high CPU utilization",
  "ActionsEnabled": true,
  "MetricName": "CPUUtilization",
  "Namespace": "AWS/EC2",
  "Statistic": "Average",
  "Period": 300,
  "Threshold": 80,
  "ComparisonOperator": "GreaterThanThreshold",
  "Dimensions": [
    {
      "Name": "InstanceId",
      "Value": "i-xxxxxxxxxxxxxx"
    }
  ]
}

```
