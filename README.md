# Network Infrastructure Design for FastPay Financial Company
<img width="1065" height="431" alt="image" src="https://github.com/user-attachments/assets/28cdfeb0-b2c0-46d4-9ed8-e5097c894a87" />

**FastPay Financial Company** is a fintech enterprise headquartered in **District 1, Ho Chi Minh City**, with two strategic branches in **Hanoi** and **Da Nang**. The company specializes in providing online payment services, which mandates an infrastructure that ensures high security, low latency, and flexible scalability to handle transaction loads.

This project focuses on designing a comprehensive **Secure Hybrid Cloud Network** that integrates the on-premise headquarters and branches with AWS cloud resources.

## 🎯 Business Requirements & Scope

### 1. Headquarters (Ho Chi Minh City)
The HQ serves as the central hub and requires the highest level of security and segmentation:
* **Network Segmentation:** Implementation of strict VLAN policies to isolate departments:
    * Accounting Department (Finance)
    * Risk Management Department
    * IT/Technical Department
* **Advanced Security:**
    * Adoption of **Zero Trust Security** principles.
    * Deployment of **IDS/IPS** (Intrusion Detection/Prevention Systems).
    * **SIEM** (Security Information and Event Management) for log analysis.
    * **Layer 7 Firewall** for application-level traffic filtering.
* **Hybrid Cloud Strategy:** Utilization of **AWS** (or GCP) to host the core Online Transaction System, ensuring high availability.
* **Wireless Network:** Segregated Wi-Fi networks for:
    * **Corporate:** Secure access for internal staff.
    * **Guest:** Isolated access with Captive Portal authentication.

### 2. Branches (Hanoi & Da Nang)
* **Inter-site Connectivity:** Secure **VPN Site-to-Site** tunnels connecting branch offices directly to the HQ Data Center.
* **Network Monitoring:** Implementation of open-source network administration tools to monitor performance and uptime across all locations.

---

## 🏗 Technical Architecture

### On-Premise Network Design (HQ & Branches)
* **VLAN Configuration:**
    * `VLAN 10`: IT Department
    * `VLAN 20`: Accounting
    * `VLAN 30`: Risk Management
    * `VLAN 40`: Guest Wi-Fi
    * `VLAN 50`: Server Farm / Data Center
* **Routing & VPN:** OSPF/BGP routing protocols for internal traffic and IPsec VPN for secure communication between HCMC, Hanoi, and Da Nang.

### Cloud Infrastructure (AWS)
* **Region:** Singapore (`ap-southeast-1`)
* **Connectivity:** AWS Site-to-Site VPN connecting the HQ Data Center to the AWS VPC.
* **Compute:** EC2 Instances hosted in Private Subnets for the Transaction System.
* **Security:** AWS WAF, Security Groups, and NACLs.

---

## 🛠 Tools & Technologies

| Category | Technology / Tool | Usage |
| :--- | :--- | :--- |
| **Cloud Provider** | AWS (Amazon Web Services) | Hosting Online Payment System |
| **Firewall** | Next-Gen Firewall (e.g., PfSense, Palo Alto) | Layer 7 filtering, VPN Endpoint |
| **Security** | Snort / Suricata | IDS/IPS |
| **Monitoring** | Zabbix / Prometheus / Grafana | Network Performance Monitoring (Open Source) |
| **SIEM** | Wazuh / ELK Stack | Log analysis and threat detection |
| **Authentication** | Radius / LDAP | Wi-Fi Captive Portal & User Auth |

## 🚀 Deployment Steps
1.  **Infrastructure Provisioning:** Set up physical/virtual routers and switches for HQ and Branches.
2.  **VLAN & Routing:** Configure internal VLANs and inter-VLAN routing.
3.  **VPN Setup:** Establish Site-to-Site VPN tunnels between Branches and HQ.
4.  **Cloud Integration:** Deploy AWS VPC and establish VPN connection to HQ.
5.  **Security Implementation:** Configure Firewall rules, IDS/IPS, and WAF.
6.  **Monitoring Setup:** Deploy monitoring agents and dashboards.

---

# Deploy Hybrid Cloud.
<img width="2690" height="1101" alt="UPDATENT113 drawio (2)" src="https://github.com/user-attachments/assets/6edc392c-4003-4b41-8f2d-7492a56bfc91" />
 
### 1. Network & Region
* **Region:** Singapore (`ap-southeast-1`).
* **VPC:** A custom Virtual Private Cloud providing network isolation.
* **Availability Zones:** Resources are distributed across `ap-southeast-1a` and `ap-southeast-1b` for fault tolerance.
* **Subnets:**
    * **Public Subnets:** Host NAT Gateways and Load Balancers.
    * **Private Subnets:** Host the application logic (Web Servers) with no direct internet access.

### 2. Compute & Scaling
* **EC2 Instances:** Web servers (`web-server-1`, `web-server-2`) running in private subnets.
* **Auto Scaling Group:** Automatically adjusts the number of EC2 instances to handle traffic spikes and ensure application availability.

### 3. Traffic Management & Security
* **AWS WAF (Web Application Firewall):** Protects the application from common web exploits (SQL injection, XSS) before traffic enters the VPC.
* **Application Load Balancer (ALB):** Distributes incoming application traffic across multiple targets (EC2 instances) in different Availability Zones.
* **NAT Gateway:** Allows instances in private subnets to connect to the internet (e.g., for updates) while preventing the internet from initiating connections with those instances.
* **Internet Gateway:** Enables communication between the VPC and the internet for public resources.

### 4. Hybrid Connectivity (Site-to-Site VPN)
* **Virtual Private Gateway (VGW):** The VPN concentrator on the AWS side.
* **Customer Gateway:** The physical or software appliance on the customer's on-premise network.
* **AWS Site-to-Site VPN:** Encrypted tunnel connecting the AWS VPC to the Customer Network (Data Center and Clients).

### 5. Observability
* **Amazon CloudWatch:** Used for monitoring resources and application logs.
* **AWS CloudTrail:** Tracks user activity and API usage for auditing.

## 🔄 Traffic Flow

### Inbound Internet Traffic
1.  **User Request:** External users attempt to access the application.
2.  **WAF Inspection:** Traffic passes through the AWS WAF for security filtering.
3.  **Load Balancing:** Valid traffic reaches the **Application Load Balancer** via the **Internet Gateway**.
4.  **Processing:** The ALB routes requests to healthy **EC2 Instances** within the Auto Scaling Group in the Private Subnets.

### Outbound Internet Traffic (from Servers)
1.  Private EC2 instances initiate a request (e.g., software update).
2.  Traffic is routed to the **NAT Gateway** located in the Public Subnet.
3.  The NAT Gateway forwards the traffic to the internet via the Internet Gateway.

### Hybrid Traffic (Internal)
1.  **Customer Network:** Traffic from the Data Center or Internal Clients.
2.  **VPN Tunnel:** Data travels securely through the AWS Site-to-Site VPN.
3.  **VGW to VPC:** Traffic enters the VPC via the Virtual Private Gateway and accesses internal resources based on route tables and Security Groups.
