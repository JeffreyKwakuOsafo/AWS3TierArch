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
<img width="949" height="833" alt="rds1" src="https://github.com/user-attachments/assets/93922cb9-e388-48eb-8f82-7e1d0cee94ee" />

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

✅ Create an Aurora replica (read node) in a different AZ.

This ensures high availability and fault tolerance.

Under VPC, select the VPC you created for this project.

Choose the DB Subnet Group you created earlier. This ensures the database will be deployed across the two Availability Zones.

For Security Group, select the Database Security Group you set up previously. This will allow access only from your private application instances.

Review the configuration and click Create Database.

At this point, AWS will provision the Aurora (MySQL-compatible) cluster in your selected VPC and subnets, secured by the database SG.
✅ Now your RDS database is fully provisioned and ready for your App Tier instances to connect.
<img width="1913" height="599" alt="rds5" src="https://github.com/user-attachments/assets/671f0400-5c35-4844-8af5-19ffc9e12355" />
