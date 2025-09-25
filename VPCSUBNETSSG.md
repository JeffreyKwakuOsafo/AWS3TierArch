
1️⃣ Create a VPC

<img width="1500" height="855" alt="create vpc" src="https://github.com/user-attachments/assets/bc60ee9b-6fd1-4dc0-9aee-ea8546576f51" />

1. Navigate to the VPC Dashboard in the AWS Management Console.
2. Click Create VPC.
3. Provide a name for the VPC.
4. Select a CIDR block (e.g., 10.0.0.0/16).
o 💡 A /16 range provides plenty of IP addresses.
5. Click Create VPC.

2️⃣ Create and Attach an Internet Gateway �
<img width="1779" height="718" alt="igw" src="https://github.com/user-attachments/assets/ed9f30a1-f892-43d3-b268-b7e8a9de287b" />�
<img width="1818" height="645" alt="attachihwtovpc" src="https://github.com/user-attachments/assets/0d996db0-4c8a-48a0-b14f-7660351bc47d" />

1. In the VPC Dashboard, select Internet Gateways.
2. Click Create Internet Gateway and give it a name.
3. Select the created IGW and click Attach to VPC.
4. Choose your VPC and confirm.

3️⃣ Create Subnets 🗂️
<img width="1678" height="654" alt="createsubnets" src="https://github.com/user-attachments/assets/c04791ab-e981-4e4a-b82f-18908d198f01" />

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

<img width="1920" height="570" alt="publicrt" src="https://github.com/user-attachments/assets/6effe8d3-d228-4c9b-9cf9-37ff3b734e63" />
<img width="1907" height="796" alt="Publicsubnetassociation" src="https://github.com/user-attachments/assets/8e586b80-d765-4807-a602-9941bfb7e9e7" />
1. Go to Route Tables and click Create Route Table.

2. Name it (e.g., Public-RT) and associate it with your VPC.

3. Select the route table → Routes → Edit Routes.

4. Add a route:
  o Destination: 0.0.0.0/0
  o Target: Internet Gateway (IGW)

5. Go to Subnet Associations → associate the route table with both public subnets.

5️⃣ Create a NAT Gateway 🔄
<img width="1543" height="806" alt="NGWAYAZ1" src="https://github.com/user-attachments/assets/eef7e91b-d6ad-46e9-b406-9502c5e482f7" />
<img width="1538" height="557" alt="privatert" src="https://github.com/user-attachments/assets/18083b5c-984e-4384-9e6c-983c345eac3b" />

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
<img width="1910" height="793" alt="resourcemap" src="https://github.com/user-attachments/assets/bdb36ce7-15d4-490f-90e9-4675bd0e37da" />
• 🛠 VPC: 10.0.0.0/16

• 🌍 Public Subnets: 2 (with IGW access)

• 🔒 Private Subnets: 4 (internet via NAT Gateway)

• 🌐 Internet Gateway: Provides public internet access

• 🔄 NAT Gateway: Allows outbound traffic for private subnets

  • 🛣 Route Tables:
  o Public → IGW
  o Private → NAT Gateway

  


🛡️ AWS SECURITY GROUPS

Security Groups (SGs) act as virtual firewalls for your Amazon EC2 instances and load balancers.
They operate at the instance level and are stateful, meaning:
• ✅ If you allow inbound traffic, the response is automatically allowed outbound.
• 🚫 You don’t need to explicitly define outbound rules for replies.
This section outlines how to configure security groups for a multi-tier VPC architecture.

🌐 External Load Balancer SG

<img width="1882" height="803" alt="externallbsg" src="https://github.com/user-attachments/assets/60734c6b-5570-452d-8152-cdabe3c9e9fb" />

• Purpose: Allows external (internet) clients to access your application and distribute incoming traffic to the web servers.
• Rules:

o Inbound:

• HTTP (80) → Source: 0.0.0.0/0

• Optionally restrict to My IP for testing.

o Outbound: Default (allowed).

🖥️ Web Tier SG

<img width="1597" height="600" alt="webtiersg" src="https://github.com/user-attachments/assets/eafdb476-eab6-4484-905e-11a391609af4" />

• Purpose: Protects web servers; only accepts traffic from the external load balancer.

• Rules:

o Inbound:

• HTTP (80) → Source: External LB SG

o Outbound: Default.

🔁 Internal Load Balancer SG

<img width="1607" height="651" alt="internallb" src="https://github.com/user-attachments/assets/b8f6c30d-4c33-4e54-9ace-c31d1fa54225" />

• Purpose: Distributes traffic between web servers and private app servers.

• Rules:

o Inbound:

• HTTP (80) → Source: Web Tier SG

o Outbound: Default.

⚙️ Private Tier SG

<img width="1610" height="667" alt="app teir sg" src="https://github.com/user-attachments/assets/40da712f-5cf2-4073-b418-6f8e23e0ac62" />

• Purpose: Protects private app servers (e.g., Node.js).

• Rules:

o Inbound:

• TCP (4000) → Source: Internal LB SG

• TCP (4000) → Source: Your IP (testing only)

o Outbound: Default.

🗄️ Database SG

<img width="1598" height="649" alt="dbsg" src="https://github.com/user-attachments/assets/48784a97-0f4d-4166-b69f-58daa1d4f9ac" />

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

