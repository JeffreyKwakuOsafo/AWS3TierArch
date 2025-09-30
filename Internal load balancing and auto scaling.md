
🚀 App Tier Setup with Auto Scaling and Internal Load Balancer
 App Tier in AWS using an internal Application Load Balancer (ALB) and an Auto Scaling Group (ASG) for a Node.js app running on port 4000.

1. 🖼️ Create an AMI of the App Tier Instance

Go to the EC2 Dashboard.

Select your App Tier Instance.

Click Actions → Image → Create Image.

Give it a name and description.

Hit Create Image → this will save a reusable AMI (Amazon Machine Image).

💡 Think of this as taking a snapshot of your app server so you can launch identical copies later.

2. 🎯 Create a Target Group

In the EC2 Dashboard, open Target Groups.

Click Create target group.

Select Instances as the target type.

Enter a friendly name.

Set Protocol: HTTP and Port: 4000 (because our Node.js app listens on 4000).

Choose your project’s VPC.

Set up health checks:

Protocol: HTTP

Path: /health

Click Create.

💡 This is how AWS knows if your app is healthy and ready to serve traffic.

3. ⚖️ Create an Internal Load Balancer

From the EC2 Dashboard, go to Load Balancers.

Click Create Load Balancer → choose Application Load Balancer (ALB).

(We use ALB since HTTP is at the application layer).

Set Scheme: Internal.

Give it a name.

Pick your project’s VPC and select two private subnets.

Attach the Internal Load Balancer Security Group.

Add a listener:

Protocol: HTTP

Port: 80

Click Next: Configure Routing.

Attach the target group you just created.

Review everything → Create Load Balancer.

💡 This acts like a traffic cop 🚦 inside your VPC, spreading requests evenly across app servers.

4. 📝 Create a Launch Template

Go to EC2 Dashboard → Launch Templates.

Click Create launch template.

Give it a name and description.

Select the AMI you created earlier.

Choose an instance type (e.g., t2.micro).

Select a key pair (⚠️ don’t hardcode it in the template).

Attach the App Tier Security Group.

Under Advanced details, set IAM instance profile → choose the EC2Role from the start of the project.

Click Create launch template.

💡 This is like a recipe 📜 for launching new servers automatically.

5. 🤖 Create an Auto Scaling Group

In the EC2 Dashboard, go to Auto Scaling Groups.

Click Create Auto Scaling group.

Enter a name.

Select the launch template from step 4.

Click Next.

Choose your project’s VPC and private subnets.

In advanced options, attach the internal load balancer.

Set the group size:

Desired: 2

Minimum: 2

Maximum: 4

Review → Create Auto Scaling group.









