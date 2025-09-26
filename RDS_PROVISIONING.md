🗄️ AWS RDS Setup

To provision an RDS instance, we first need to configure a Subnet Group. This tells RDS where (which subnets) to create the database.

1️⃣ Create a Subnet Group

Navigate to Subnet Groups in the RDS dashboard.

Click Create DB Subnet Group.

Select your VPC.

Choose the two Availability Zones (AZ1 and AZ2) where your subnets are located.

Select the database subnets created in each AZ (one in AZ1, one in AZ2).

Save the subnet group.

2️⃣ Create an RDS Database

Navigate to Databases in the RDS dashboard.

Click Create Database.

Choose Standard Create.

3️⃣ Select Database Engine ⚙️

For engine type, select:

Amazon Aurora (MySQL-compatible)

For template, choose:

Dev/Test (for this project)

4️⃣ Configure Database Credentials 🔐

Keep the DB cluster identifier and master username as default.

For credentials management, select Self-managed passwords.

⚠️ Note: For production workloads, use AWS Secrets Manager to manage and rotate database credentials automatically.

5️⃣ Storage Configuration 💾

Maintain the cluster storage configuration as default.

6️⃣ Availability and Durability 🌍

Under Multi-AZ deployment, check:

✅ Create an Aurora replica (read node) in a different AZ.

This ensures high availability and fault tolerance.