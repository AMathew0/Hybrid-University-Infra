# 🔍 **Monitoring, Logging & Alerting Design**

🎯 **Goal**: Ensure **complete observability** across your infrastructure for performance, uptime, failures, and security incidents using open-source and enterprise-ready tools.

---

## 🧱 Architecture Overview

| Layer           | Tool/Stack                 | Purpose                                  |
| --------------- | -------------------------- | ---------------------------------------- |
| Infrastructure  | **Zabbix 6.4 LTS**         | Server, network, VM, firewall metrics    |
| Logs            | **Graylog 5**              | Centralized log aggregation and search   |
| Visualization   | **Grafana + Prometheus**   | Real-time dashboards                     |
| Cloud Resources | **AWS CloudWatch**         | AWS-specific metrics & alarms            |
| Alerting        | Zabbix + Graylog + Grafana | Multi-channel alerts (email, Slack, SMS) |

---

## 🔁 Monitoring Flow

```text
1. Zabbix agent installed on all Linux/Windows servers
2. SNMP used for printers, switches, NAS, firewalls
3. Prometheus scrapes Docker, Kubernetes, and Node Exporter metrics
4. Grafana connects to Prometheus and Zabbix for dashboards
5. Graylog receives logs from syslog (Linux), Winlogbeat (Windows), FortiGate, and AD
6. Alerts configured in Zabbix and Graylog (email + Telegram/Slack)
```

---

## 📈 Zabbix Monitoring

| Monitored Asset          | Method       | Notes                              |
| ------------------------ | ------------ | ---------------------------------- |
| Servers (Linux/Win)      | Zabbix Agent | CPU, memory, disk, process, uptime |
| Network Devices (Switch) | SNMP         | Port status, errors, traffic       |
| FortiGate Firewall       | SNMP / API   | Sessions, VPN, dropped packets     |
| VMs & Hypervisors        | Agent + API  | VM status, host load, I/O          |
| Website/Uptime           | ICMP/HTTP    | Website health checks              |
| Applications (Moodle)    | HTTP/DB/Log  | HTTP response, DB connections      |

* 🧰 **Custom Templates**: Moodle, PostgreSQL, Docker, Samba, Proxmox
* 🧠 **Triggers**: CPU > 90%, disk < 10%, LDAP failure, Zabbix agent unreachable

---

## 📊 Prometheus + Grafana

| Metric Type       | Source              | Visualized in |
| ----------------- | ------------------- | ------------- |
| Node/VM stats     | Node Exporter       | Grafana       |
| Docker containers | cAdvisor            | Grafana       |
| K8s clusters      | kube-prometheus     | Grafana       |
| Database metrics  | PostgreSQL Exporter | Grafana       |

📌 Use Grafana to unify visualizations from both Zabbix and Prometheus for better dashboarding.

---

## 📄 Centralized Logging with Graylog

| Source           | Collector        | Notes                                |
| ---------------- | ---------------- | ------------------------------------ |
| Linux Servers    | rsyslog          | Logs over TCP to Graylog             |
| Windows Servers  | Winlogbeat       | Event logs sent to Graylog           |
| FortiGate        | Syslog UDP       | Security logs, blocked IPs, VPN logs |
| Active Directory | Winlogbeat + GPO | Auth logs, group policy changes      |
| Applications     | Filebeat         | LMS logs, Docker app logs            |

🔐 **Enrichment**: Tag logs by hostname, site, severity
🔎 **Dashboards**: Login failures, firewall denies, kernel errors

---

## 🚨 Alerting & Notifications

| Tool       | Type of Alert                    | Channels             |
| ---------- | -------------------------------- | -------------------- |
| Zabbix     | Host down, high CPU, disk full   | Email, Telegram      |
| Graylog    | Auth failures, firewall logs     | Email, Slack Webhook |
| Grafana    | Metric-based thresholds          | Slack, Web UI popup  |
| CloudWatch | AWS alarms (EC2, RDS, S3 events) | AWS SNS to email/SMS |

📌 **Integration Tip**: Use webhooks for Slack/Telegram.
📦 **Notification Tools**: Pushover, Gotify, OpsGenie (optional)

---

## 🎯 Dashboard Examples

| Tool       | Dashboard Purpose                  |
| ---------- | ---------------------------------- |
| Grafana    | Real-time server metrics + uptime  |
| Zabbix     | Device health, service monitoring  |
| Graylog    | Login failures, app error patterns |
| CloudWatch | AWS usage, EC2 health, billing     |

---

## 🧪 Example Use Case

**Login Failure Surge (Brute Force Attack)**

```text
1. Graylog detects spike in failed SSH logins
2. Trigger alert: >20 failures in 5 mins
3. Send Slack alert + auto block via Ansible (fail2ban update)
4. Zabbix updates firewall status + shows block confirmation
5. Log preserved for forensics (Elastic backend optional)
```

---

## 🛡️ Best Practices

* ⏱️ Set up backup monitoring servers in other sites (HA)
* 🧪 Test alerts every month (simulate outage)
* 🔐 Use encrypted transport (TLS) for agent and syslog
* 💽 Store logs offsite (S3 + Glacier Deep Archive)
* ⚙️ Enable versioning and alert on config changes (Zabbix, Graylog)

---

## 🔐 Compliance

| Standard        | Compliance Action                        |
| --------------- | ---------------------------------------- |
| FERPA/GDPR      | Mask PII in logs, rotate & delete policy |
| ISO 27001       | Evidence of monitoring + alert workflow  |
| Internal Audits | Export reports from Zabbix & Graylog     |

---

## 🧰 Open Source Stack Summary

| Category           | Tool                      | License    |
| ------------------ | ------------------------- | ---------- |
| Monitoring         | Zabbix                    | GPL        |
| Logging            | Graylog OSS               | GPL        |
| Visualization      | Grafana                   | AGPL       |
| Metrics Collectors | Prometheus, Node Exporter | Apache 2.0 |
| Cloud              | AWS CloudWatch            | AWS-native |

---
