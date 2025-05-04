# Cloud Platforms Strategy – Hybrid University Infrastructure

This section outlines the hybrid cloud design for the imaginary university. A multi-cloud strategy is adopted to utilize the strengths of AWS, Azure, and GCP in alignment with departmental and research needs.

## Goals

- Distribute workloads based on strengths of each cloud
- Improve availability and disaster recovery
- Reduce vendor lock-in
- Integrate cloud with on-premises systems

## Roles of Cloud Providers

| Provider | Role |
|---------|------|
| AWS     | Web hosting, backup storage, S3-based archives, serverless apps |
| Azure   | Identity (Azure AD), Microsoft 365 integration, student/staff apps |
| GCP     | Research & data science workloads, BigQuery, AI/ML |

## Cloud Interconnect

To ensure seamless hybrid connectivity, the following setup is implemented across all three cloud platforms (AWS, Azure, GCP) and the on-premises data center:

### 🔐 Connectivity

- **Primary Method:** Site-to-Site IPsec VPN tunnels from the on-premises FortiGate firewall to:
  - AWS VPN Gateway
  - Azure VPN Gateway
  - GCP Cloud VPN
- **Alternate Method (Large Campus/Research Sites):** Fortinet SD-WAN for dynamic routing, application-aware traffic shaping, and link redundancy.

### 🌐 Network Segmentation

- **Virtual Network Segments (Cloud):**
  - VPC/VNet/Subnets per function (e.g., DMZ, App, DB)
  - Firewall policies enforce zone-to-zone communication only as needed.
- **On-Prem ↔ Cloud Routing:**
  - BGP-based dynamic routing for failover and scalability
  - Static routes for low-change environments

### 🧭 DNS Integration

- **Cloud DNS Providers:**
  - AWS Route 53 (private + public zones)
  - Azure Private DNS
  - GCP Cloud DNS
- **Hybrid DNS Setup:**
  - Split-horizon DNS with conditional forwarders from on-premises AD DNS to cloud DNS zones.
  - Internal hostnames resolve to private IPs within hybrid VPC/VNet.
  - External DNS uses public zones hosted on Route 53 for university domains (e.g., `*.university.edu`).

This document outlines how AWS, Azure, and GCP are integrated with our on-premises university data center in a secure, resilient, and highly segmented hybrid network design.

---

## 🔐 VPN & SD-WAN Connectivity

All cloud environments are connected to the university's on-premises data center through IPsec VPN tunnels or SD-WAN for dynamic routing and high availability.

### Primary IPsec VPN Connections

| Platform | Gateway Name     | Tunnel Type | Termination Point (Cloud) | On-Prem Endpoint         |
|----------|------------------|-------------|----------------------------|---------------------------|
| AWS      | `univ-vpn-gw1`   | IPsec VPN   | AWS Virtual Private Gateway | FortiGate-100D @ DC1      |
| Azure    | `az-vnet-gateway`| IPsec VPN   | Azure VPN Gateway          | FortiGate-200D @ DC1      |
| GCP      | `gcp-vpn-hub`    | IPsec VPN   | GCP Cloud VPN              | FortiGate-200D @ DC2      |

> Each IPsec tunnel uses IKEv2 and AES-256 encryption.

### Optional SD-WAN (for remote campuses)

- **Fortinet SD-WAN** enabled between:
  - Campus A (India)
  - Campus B (Middle East)
  - On-premises DC
- **Use Case:** Smart routing for Zoom, Microsoft 365, LMS access.

---

## 🔁 Network Segmentation and Routing

We enforce security through **zone-based firewall policies** and **subnet-level segmentation**.

### Example: AWS VPC Layout

| Subnet Type | CIDR Range       | Purpose               | Firewall Policy (Sample)       |
|-------------|------------------|------------------------|---------------------------------|
| DMZ         | 10.10.1.0/24     | Public-facing services | Allow HTTP/HTTPS from Internet |
| App         | 10.10.2.0/24     | LMS, internal APIs     | Allow only from DMZ            |
| DB          | 10.10.3.0/24     | Database layer         | Allow only from App subnet     |

> All subnets use **NACLs** and **Security Groups** to add an extra security layer.

### Routing Example (BGP)

- On-prem FortiGate advertises 192.168.0.0/16 to cloud via BGP.
- AWS VPC advertises 10.10.0.0/16 to on-prem.
- BGP is used over IPsec tunnels for dynamic failover.

---

## 🧭 DNS Design – Internal and External

We use a **split-horizon DNS** approach to manage internal and external name resolution.

### Internal DNS (Hybrid)

- **On-Prem DNS (AD DS):** `univ.local`
- **AWS Route 53 Private Hosted Zone:** `aws.univ.local`
- **Azure Private DNS Zone:** `azure.univ.local`
- **GCP DNS Zone:** `gcp.univ.local`

> Conditional forwarders are set up on the on-prem AD DNS to forward requests to each cloud’s internal DNS.

### External DNS (Public)

- **Hosted on AWS Route 53**
- Domain: `university.edu`
- Handles:
  - `www.university.edu`
  - `studentportal.university.edu`
  - `api.university.edu`

---

## 🔐 Security Controls Summary

| Component               | Technology Used                  | Notes                                        |
|------------------------|----------------------------------|----------------------------------------------|
| VPN Encryption         | AES-256 / IKEv2                  | Enforced on all tunnels                      |
| Cloud Firewalls        | AWS SG + NACL, Azure NSG, GCP FW | Applied per subnet/service                   |
| Central Firewall Logs  | FortiAnalyzer + ELK/Wazuh        | Logs collected from all FortiGates + clouds  |
| Threat Monitoring      | Azure Sentinel (SIEM)            | For alerting, anomaly detection              |

---

## ✅ Best Practices Summary

- Use **BGP** for dynamic failover and route propagation.
- Enforce **zero trust between zones** – allow only required traffic.
- Use **conditional forwarders** in DNS to resolve internal hybrid services.

### 🔐 Security Integration

- Firewall-to-firewall tunnels are encrypted with AES-256
- Cloud-native firewalls (NSGs, GCP firewall rules) limit inbound/outbound by IP and port
- All traffic logs are exported to SIEM (hosted on-prem or in cloud, e.g., Azure Sentinel or self-hosted Wazuh)
