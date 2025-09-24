#                                                                                                                                AWS3TierArch

##################🏗️ AWS Three-Tier Web Architecture Project Overview################


This project is a hands-on walkthrough of deploying a three-tier web architecture on AWS.
You’ll manually build and configure each component required to run a secure, highly available, and scalable application.

🔑 Learning Objectives

By completing this project, you will learn how to:

Design and deploy networking – VPC, subnets, routing tables, and internet/NAT gateways.

Implement security – configure security groups, NACLs, and IAM roles.

Set up the application tier – launch EC2 instances and configure a load balancer.

Provision the database tier – deploy and secure a managed database service (e.g., Amazon RDS).

Test availability & scalability – confirm the architecture supports growth and resilience.

🧰 Prerequisites

An active AWS account

IAM user with sufficient permissions

Basic knowledge of networking, Linux, and AWS services

📝 Workshop Steps

Networking Layer

Create a VPC

Add public and private subnets

Configure internet and NAT gateways

Security Layer

Set up security groups for web, app, and database tiers

Apply NACLs for subnet-level protection

Assign IAM roles for EC2 and database access

Application Layer

Launch EC2 instances in the private subnets

Configure an Application Load Balancer (ALB)

Deploy a sample web application

Database Layer

Launch an RDS instance in private subnets

Configure parameter groups, backups, and multi-AZ

Validation

Access the application through the ALB DNS
