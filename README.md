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
