
⚙️ App Tier Deployment

In this step, we set up the application tier of our 3-tier architecture.
This tier runs on EC2 instances inside private subnets, connects to the database, and hosts our backend app.
Instead of using SSH keys, we’ll use IAM roles and SSM, which is safer and more modern.

Create an EC2 Role 🔑
<img width="1500" height="403" alt="role" src="https://github.com/user-attachments/assets/a67c5201-c6e2-4a30-919f-1b63e3f4913f" />
<img width="1489" height="442" alt="role1" src="https://github.com/user-attachments/assets/137c5ed9-739e-463b-8d77-5ee2ca0e175f" />


Before launching the instance, give it the right permissions:

Go to IAM → Roles.

Create a new role and attach:

AmazonS3FullAccess (so the instance can pull code/configs from S3)

AmazonSSMManagedInstanceCore (so you can manage it without SSH)

This role acts like a keycard — it lets the instance access only what it needs.

Launch the EC2 Instance 🖥️

Go to EC2 → Launch Instance.

Name your instance (for example: app-tier-instance).

Select Amazon Linux as the AMI.

Skip key pairs (since we’ll use SSM).

Under Networking:
<img width="1671" height="887" alt="apptier1" src="https://github.com/user-attachments/assets/117ac825-db54-4eba-b5e7-bdc6d098f67c" />

Choose your project VPC.

Place it in a private subnet (AZ1).

Attach the Private Instance Security Group.

In Advanced settings, select the IAM role you just created.
<img width="1668" height="890" alt="apptier2" src="https://github.com/user-attachments/assets/c55fc94b-fac2-426d-8f55-7c980d8c951e" />

Connect to your instance via Session Manager

<img width="1904" height="634" alt="apptier3" src="https://github.com/user-attachments/assets/79270a10-dec6-4484-9898-f9dd334b18a8" />

Run the command sudo -su ec2-user
Ping the Google DNS IP on 8.8.8.8 to confirm internet connectivity


Install the MySQL Client 💽
We’ll need the MySQL client to talk to the database. Run these commands on the instance:

sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

sudo yum install mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022

sudo yum install mysql -y

mysql -h <DB-ENDPOINT> -u admin -p (h flag = your rds writer endpoint; u = database username; p = password)

After the initial database connection, we create a database schema, tables and populate by running the following commands.

CREATE DATABASE webappdb;   

SHOW DATABASES;

USE webappdb;    

CREATE TABLE IF NOT EXISTS transactions(id INT NOT NULL


AUTO_INCREMENT, amount DECIMAL(10,2), description

VARCHAR(100), PRIMARY KEY(id));    

SHOW TABLES;    

INSERT INTO transactions (amount,description) VALUES ('400','groceries');   

SELECT * FROM transactions;

If you see a MySQL prompt after entering your password, you’re in! 🎉
<img width="1546" height="498" alt="connecting to rds instance" src="https://github.com/user-attachments/assets/0e498ec5-f359-4e93-8729-a949928b1f62" />

Configure Database Settings 📝
Your app needs to know how to reach the database.

Edit the dbConfig.js file in the app-tier folder.

Add your database endpoint, username, password, and database name.

Create S3 bucket and upload this file to your S3 bucket, along with the entire app-tier folder.
<img width="1920" height="721" alt="create s3 bucket" src="https://github.com/user-attachments/assets/f915ed57-a1f9-4087-9a9b-2b4764781e8b" />


Install Node.js and PM2 ⚡
Our backend is written in Node.js. Let’s set that up:

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh
 | bash
source ~/.bashrc
nvm install 16
nvm use 16

Then install PM2, which keeps Node apps running even if the instance restarts:

npm install -g pm2

Download and Run the App 📦

Switch to home directory:

cd ~

Download the app-tier code from S3:

aws s3 cp s3://bucketname/app-tier/ app-tier --recursive

Navigate and install dependencies:

cd ~/app-tier
npm install

Start the app with PM2:

pm2 start index.js

Check if it’s working:

pm2 list

If it says “online,” you’re good.

If it says “errored,” it usually means your dbConfig.js wasn’t downloaded unto your apptier instance.
Download the config, then restart with pm2 start index.js and pm2 list.


Keep the App Running After Reboot 🔄
Run:

pm2 startup

PM2 will give you a long command (starting with sudo env PATH=...). Copy and run it, then save the config:

pm2 save

Test the Application ✅

Check if the app itself is running:

curl http://localhost:4000/health

Expected output:
This is the health check

Check if the app can talk to the database:

curl http://localhost:4000/transaction

Expected output:

{"result":[
{"id":1,"amount":400,"description":"groceries"},
{"id":2,"amount":100,"description":"class"},
{"id":3,"amount":200,"description":"other groceries"},
{"id":4,"amount":10,"description":"brownies"}
]}

If you see an HTML error page instead of JSON, check your security groups — the app probably can’t reach the database.
