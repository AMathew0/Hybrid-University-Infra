# 📈 Monitoring, Logging & Alerts – Hybrid University

This document outlines the monitoring, observability, and alerting infrastructure implemented at Hybrid University to maintain visibility, troubleshoot issues, and ensure performance compliance across all systems.

---

## 🎯 Objectives

- Real-time monitoring of network, server, cloud, and application performance
- Centralized logging for forensic analysis and compliance audits
- Proactive alerting to prevent downtime and service disruptions
- Visual dashboards for IT administrators and executives

---

## 🧰 Tools Overview

| Tool                 | Purpose                             | Environment Scope                      |
|----------------------|-------------------------------------|----------------------------------------|
| **Zabbix 6.4 LTS**    | Server & network infrastructure monitoring | On-prem & hybrid (VMs, switches, etc.) |
| **Grafana + Prometheus** | Visualization and metrics collection     | Metrics from Zabbix, Linux, and cloud  |
| **Graylog 5**         | Centralized logging (SIEM-lite)     | Syslogs from servers, firewalls, apps  |
| **AWS CloudWatch**    | Cloud-native resource monitoring     | AWS EC2, RDS, Lambda, S3, etc.         |

---

## 🖥️ Zabbix 6.4 LTS – Core Monitoring

### Key Features

- Monitors: Servers (Linux/Windows), switches, firewalls, printers
- Agent-based and SNMP-based integrations
- Low-level discovery for disks, network interfaces, services
- Alerting via email and future Telegram webhook integration

### Deployment Notes

- Installed on Ubuntu 24.04 LTS VM
- Integrated with MySQL + NGINX
- Custom templates for FortiGate, Cisco, Proxmox

---

## 📊 Grafana + Prometheus – Dashboards

### Setup

- Grafana pulls metrics from Prometheus (for Linux nodes)
- Additional datasource: Zabbix plugin for Grafana

### Use Cases

- Visualize CPU, memory, disk, bandwidth, uptime
- Executive dashboard for Uptime SLAs and server health
- Alert thresholds configured using Prometheus alert rules

---

## 📄 Graylog 5 – Centralized Log Management

### Features

- Collects logs from:
  - Linux servers (`rsyslog`)
  - Windows servers via NXLog
  - Firewalls (FortiGate, Cisco), proxy logs
- Supports GELF, syslog, JSON formats
- Searchable archives for compliance and investigations

### Deployment

- Runs on separate Ubuntu VM
- MongoDB + OpenSearch backend
- Role-based access controls (RBAC) for auditors/admins

---

## ☁️ AWS CloudWatch

### Use Cases

- EC2 metrics: CPU, disk, memory (via CloudWatch Agent)
- RDS performance metrics and backup status
- CloudWatch Alarms for EC2 instance states and usage spikes
- AWS Backup job status and health alerts

---

## 🔔 Alerting Strategy

| Source       | Tool Used      | Notification Channel     |
|--------------|----------------|---------------------------|
| Zabbix       | Internal alert system | Email, Telegram Webhook (planned) |
| Prometheus   | Alertmanager (future) | Email, Slack (planned)           |
| Graylog      | Stream alerts        | Email, Syslog forwarders         |
| CloudWatch   | CloudWatch Alarms    | AWS SNS → Email                   |

---

## 🔐 Security & Compliance

- Logs are encrypted at rest (Graylog/OpenSearch)
- Access to monitoring platforms restricted via LDAP/SSO (where supported)
- Audit logs retained for 1 year, archived to AWS Glacier

---

## ✅ Summary Table

| Tool             | Function                | Scope                    | Alerting Integrated |
|------------------|-------------------------|--------------------------|----------------------|
| Zabbix 6.4       | Infra monitoring        | On-prem, VM, LAN         | Yes                  |
| Grafana + Prometheus | Dashboards + metrics | Infra + App monitoring   | Planned              |
| Graylog 5        | Central log management  | Multi-platform logs      | Yes                  |
| AWS CloudWatch   | AWS resource monitoring | Cloud-native services    | Yes                  |

---

## 📌 Conclusion

Hybrid University's monitoring and logging strategy combines open-source observability tools with cloud-native telemetry to ensure real-time visibility and resilient operations. The integrated stack supports both IT and security operations, delivering a comprehensive view into infrastructure health and compliance.
