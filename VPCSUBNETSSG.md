
1️⃣ Create a VPC
1. Navigate to the VPC Dashboard in the AWS Management Console.
2. Click Create VPC.
3. Provide a name for the VPC.
4. Select a CIDR block (e.g., 10.0.0.0/16).
o 💡 A /16 range provides plenty of IP addresses.
5. Click Create VPC.

2️⃣ Create and Attach an Internet Gateway 🌐
1. In the VPC Dashboard, select Internet Gateways.
2. Click Create Internet Gateway and give it a name.
3. Select the created IGW and click Attach to VPC.
4. Choose your VPC and confirm.

3️⃣ Create Subnets 🗂️
We’ll create 6 subnets across two Availability Zones for high availability:
• 🌍 Public Subnets: 2 (one in each AZ)
• 🔒 Private Subnets: 4 (two per AZ, for different application tiers)
Steps:
1. Navigate to Subnets.
2. Click Create Subnet.
3. Choose the VPC you created.
4. Assign AZ1 for the first public subnet and provide a CIDR (e.g., 10.0.1.0/24).
5. Repeat for AZ2 with a different CIDR (e.g., 10.0.2.0/24).
6. Repeat the same process to create 4 private subnets (e.g., 10.0.3.0/24, 10.0.4.0/24, etc.).

4️⃣ Configure Public Route Table 🛣️
1. Go to Route Tables and click Create Route Table.
2. Name it (e.g., Public-RT) and associate it with your VPC.
3. Select the route table → Routes → Edit Routes.
4. Add a route:
o Destination: 0.0.0.0/0
o Target: Internet Gateway (IGW)
5. Go to Subnet Associations → associate the route table with both public subnets.

5️⃣ Create a NAT Gateway 🔄
Private instances need outbound internet (for updates, patches, etc.) but should not allow inbound traffic. A NAT Gateway handles this.
1. Go to NAT Gateways → click Create NAT Gateway.
2. Place it inside a public subnet.
3. Allocate and attach an Elastic IP Address.
4. Click Create NAT Gateway.

6️⃣ Configure Private Route Table 🔐
1. Create a new route table (e.g., Private-RT).
2. Edit its routes:
o Destination: 0.0.0.0/0
o Target: NAT Gateway
3. Go to Subnet Associations → associate it with your private subnets.
o 🔁 Repeat for both private subnets across AZs for high availability.

7️⃣ Summary Architecture 🏗️
• 🛠 VPC: 10.0.0.0/16
• 🌍 Public Subnets: 2 (with IGW access)
• 🔒 Private Subnets: 4 (internet via NAT Gateway)
• 🌐 Internet Gateway: Provides public internet access
• 🔄 NAT Gateway: Allows outbound traffic for private subnets
• 🛣 Route Tables:
o Public → IGW
o Private → NAT Gateway

🛡️ AWS Security Groups Setup
Security Groups (SGs) act as virtual firewalls for your Amazon EC2 instances and load balancers.
They operate at the instance level and are stateful, meaning:
• ✅ If you allow inbound traffic, the response is automatically allowed outbound.
• 🚫 You don’t need to explicitly define outbound rules for replies.
This section outlines how to configure security groups for a multi-tier VPC architecture.

🌐 External Load Balancer SG
• Purpose: Allows external (internet) clients to access your application and distribute incoming traffic to the web servers.
• Rules:
o Inbound:
• HTTP (80) → Source: 0.0.0.0/0
• Optionally restrict to My IP for testing.
o Outbound: Default (allowed).

🖥️ Web Tier SG
• Purpose: Protects web servers; only accepts traffic from the external load balancer.
• Rules:
o Inbound:
• HTTP (80) → Source: External LB SG
o Outbound: Default.

🔁 Internal Load Balancer SG
• Purpose: Distributes traffic between web servers and private app servers.
• Rules:
o Inbound:
• HTTP (80) → Source: Web Tier SG
o Outbound: Default.

⚙️ Private Application SG
• Purpose: Protects private app servers (e.g., Node.js).
• Rules:
o Inbound:
• TCP (4000) → Source: Internal LB SG
• TCP (4000) → Source: Your IP (testing only)
o Outbound: Default.

🗄️ Database SG
• Purpose: Protects database servers; only accepts connections from private app servers.
• Rules:
o Inbound:
• MySQL/Aurora (3306) → Source: Private App SG
o Outbound: Default.

📝 Security Group Flow Overview
• 🌐 External LB SG → Internet traffic (HTTP)
• 🖥️ Web Tier SG → From External LB SG
• 🔁 Internal LB SG → From Web Tier SG
• ⚙️ Private App SG → From Internal LB SG + Your IP (test)
• 🗄️ Database SG → From Private App SG
✅ This enforces the principle of least privilege while maintaining high availability.

