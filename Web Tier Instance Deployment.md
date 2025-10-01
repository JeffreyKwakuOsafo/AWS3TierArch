🌐 Web Tier Configuration


1. Update nginx.conf in Application Code
   
<img width="1376" height="774" alt="internallbproxy" src="https://github.com/user-attachments/assets/ad9a5142-f2b6-4a37-88cb-39102b9f72d7" />

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
   
<img width="1920" height="1080" alt="Screenshot (1936)" src="https://github.com/user-attachments/assets/d2c41322-a522-4896-8d9d-b64703b8bfd7" />

Connect to the instance using SSM Session Manager.

Verify internet connectivity:

ping 8.8.8.8


Install Node.js v16 using NVM:

<img width="1920" height="1080" alt="Screenshot (1937)" src="https://github.com/user-attachments/assets/fd6aa9bc-23f0-491d-8a7d-5207ddc8891e" />

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
source ~/.bashrc
nvm install 16
nvm use 16

4. Download & Build Web Tier Code

<img width="1920" height="1080" alt="Screenshot (1937)" src="https://github.com/user-attachments/assets/fd6aa9bc-23f0-491d-8a7d-5207ddc8891e" />

Go to the home directory:

cd ~


Download code from S3:

aws s3 cp s3://BUCKET_NAME/web-tier/ web-tier --recursive


Install dependencies and build:

<img width="1920" height="1080" alt="Screenshot (1938)" src="https://github.com/user-attachments/assets/d6804c35-7857-4454-a301-2b5d9b4ba290" />

cd ~/web-tier
npm install
npm run build

5. Install & Configure Nginx

Install Nginx:

sudo yum install nginx


Navigate to Nginx config directory:

cd /etc/nginx


Replace nginx.conf with the updated version:

sudo rm nginx.conf
sudo aws s3 cp s3://BUCKET_NAME/nginx.conf .

6. Start and Enable Nginx
   
<img width="1920" height="1080" alt="Screenshot (1940)" src="https://github.com/user-attachments/assets/04d84f8f-78fe-4770-9051-452f1528f20f" />

Restart and check Nginx:

sudo service nginx restart
sudo service nginx status


Grant necessary file permissions:

chmod -R 755 /home/ec2-user


Enable Nginx to run on boot:

sudo chkconfig nginx on
