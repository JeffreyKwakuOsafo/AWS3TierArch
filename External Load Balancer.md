
🚀 Web Tier Setup with Auto Scaling and External Load Balancer
 App Tier in AWS using an External Application Load Balancer (ALB) and an Auto Scaling Group (ASG) 

1. 🖼️ Create an AMI of the App Tier Instance

Go to the EC2 Dashboard.

Select your Web Tier Instance.

Click Actions → Image → Create Image.

Give it a name and description.

Hit Create Image → this will save a reusable AMI (Amazon Machine Image).



2. 🎯 Create a Target Group

In the EC2 Dashboard, open Target Groups.

Click Create target group.

Select Instances as the target type.

Enter a friendly name.

Set Protocol: HTTP and Port: 80 (HTTP works on port 80).

Choose your project’s VPC.

Set up health checks:

Protocol: HTTP

Path: /health

Click Create.


3. ⚖️ Create an External Load Balancer
<img width="1920" height="1080" alt="Screenshot (1942)" src="https://github.com/user-attachments/assets/632812a3-b570-4a81-9e0d-30d5969ab699" />

From the EC2 Dashboard, go to Load Balancers.

Click Create Load Balancer → choose Application Load Balancer (ALB).

(We use ALB since HTTP is at the application layer).

Set Scheme: Internet-facing.

Give it a name.

Pick your project’s VPC and select two public subnets.

Attach the External Load Balancer Security Group.

Add a listener:

Protocol: HTTP

Port: 80

Click Next: Configure Routing.

Attach the target group you just created.

Review everything → Create Load Balancer.


4. 📝 Create a Launch Template

Go to EC2 Dashboard → Launch Templates.


Click Create launch template.

Give it a name and description.

Select the AMI you created earlier.

Choose an instance type (e.g., t2.micro).

Attach the Web Tier Security Group.

Under Advanced details, set IAM instance profile → choose the EC2Role from the start of the project.

Click Create launch template.

5. 🤖 Create an Auto Scaling Group
 


In the EC2 Dashboard, go to Auto Scaling Groups.

Click Create Auto Scaling group.

Enter a name.

Select the launch template from step 4.

Click Next.

Choose your project’s VPC and public subnets.

In advanced options, attach the external load balancer.

Set the group size:

Desired: 2

Minimum: 2

Maximum: 4

Review → Create Auto Scaling group.

Navigate to the external load balancer details, copy and paste external load balancer DNS name into browser


<img width="1920" height="1080" alt="Screenshot (1946)" src="https://github.com/user-attachments/assets/71ca887c-c5dd-4ded-9338-bd8c05bb8697" />


<img width="1920" height="1080" alt="Screenshot (1947)" src="https://github.com/user-attachments/assets/947962e8-c380-4f1b-add5-91ff99ca54ea" />


<img width="1920" height="1080" alt="Screenshot (1948)" src="https://github.com/user-attachments/assets/5d237b82-5258-4673-b979-0a77a0f4edd3" />
