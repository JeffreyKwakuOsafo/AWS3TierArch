🌐 Web Tier Configuration


1. Update nginx.conf in Application Code

Open the file:

application-code/nginx.conf


Locate line 58 (the proxy_pass line).

Replace everything inside the square brackets [...] with the Internal Load Balancer DNS.

Find the DNS on the Internal Load Balancer details page.

Save the file and upload it to your S3 bucket.

2. Launch Web Tier Instances

Launch a new EC2 instance (repeat the same steps as for the AppTier).

In the Networking section:

Select the Project’s VPC

Choose a Public Subnet

Enable Auto-assign Public IP

Attach the Web Tier Security Group.

Complete the rest of the steps as in the AppTier setup.

3. Verify Connectivity & Install Node.js

Connect to the instance using SSM Session Manager.

Verify internet connectivity:

ping 8.8.8.8


Install Node.js v16 using NVM:

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc
nvm install 16
nvm use 16

4. Download & Build Web Tier Code

Go to the home directory:

cd ~


Download code from S3:

aws s3 cp s3://BUCKET_NAME/web-tier/ web-tier --recursive


Install dependencies and build:

cd ~/web-tier
npm install
npm run build

5. Install & Configure Nginx

Install Nginx:

sudo yum install nginx -y


Navigate to Nginx config directory:

cd /etc/nginx


Replace nginx.conf with the updated version:

sudo rm nginx.conf
sudo aws s3 cp s3://BUCKET_NAME/nginx.conf .

6. Start and Enable Nginx

Restart and check Nginx:

sudo service nginx restart
sudo service nginx status


Grant necessary file permissions:

chmod -R 755 /home/ec2-user


Enable Nginx to run on boot:

sudo chkconfig nginx on