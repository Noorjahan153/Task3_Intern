**Task 3: Strapi Setup on AWS EC2 Using Terraform**  
**Overview**

This project demonstrates deploying Strapi (Headless CMS) on AWS EC2 using Terraform. Access to the EC2 instance is secured using a .pem key, and the project includes steps to install Node.js, clone the Strapi repo, build the admin panel, and run Strapi on port 1337.

1️⃣ Prerequisites

AWS account with IAM user credentials

Terraform installed locally

.pem key for SSH access

Node.js (v18+) and npm installed on the EC2 instance

Git installed on the EC2 instance

2️⃣ Terraform Configuration
Create a main.tf file for EC2 instance, security group, and key pair:

provider "aws" {
  region = "ap-south-1"
}

# Create a key pair
resource "aws_key_pair" "strapi_key" {
  key_name   = "strapi-key"
  public_key = file("~/.ssh/strapi-key.pub")
}

# Security group allowing SSH and Strapi port
resource "aws_security_group" "strapi_sg" {
  name        = "strapi-sg"
  description = "Allow SSH and Strapi"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 1337
    to_port     = 1337
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# EC2 instance
resource "aws_instance" "strapi_ec2" {
  ami                    = "ami-0abcdef1234567890" # Amazon Linux 2023 AMI
  instance_type          = "t3.medium"
  key_name               = aws_key_pair.strapi_key.key_name
  vpc_security_group_ids = [aws_security_group.strapi_sg.id]

  tags = {
    Name = "Strapi-EC2"
  }
}

Initialize and Apply Terraform

Aws configure - Connected through SSH Key  
terraform init
terraform plan
terraform apply
terraform validate
terraform show
terraform destroy

# Strapi Setup on AWS EC2

## Strapi Admin Panel
Access your Strapi admin panel here:  
[http://18.61.211.250:1337/admin](http://18.61.211.250:1337/admin)

## Steps to deploy Strapi using .pem file

### 1️⃣ Connect to EC2
```bash
ssh -i strapi-key.pem ec2-user@18.61.211.250

2️⃣ Update and Install Dependencies

sudo yum update -y
sudo yum install -y git make gcc gcc-c++ python3
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo yum install -y nodejs

3️⃣ Clone Project
git clone https://github.com/Noorjahan153/Task1_Intern.git strapi
cd strapi

4️⃣ Install Node Modules
npm install

### 5️⃣ If memory errors during build
export NODE_OPTIONS="--max-old-space-size=2048"

### 6️⃣ Configure Environment Variables
nano .env

Add:

APP_KEYS=1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d,7f6e5d4c3b2a19081726354455667788
ADMIN_JWT_SECRET=4d2f1a9b0e6c7f8a9b1c2d3e4f5a6b7c
JWT_SECRET=6iMiAxWTcBVuvLAkHJT0BQ==
API_TOKEN_SALT=9jVj3nXwudlsEtl7u1Pcmg==
HOST=0.0.0.0

Save with Ctrl+O, Enter, Ctrl+X.

### 7️⃣ Build Admin Panel
npm run build

### 8️⃣ Start Strapi
nohup npm start > strapi.log 2>&1 &
tail -f strapi.log

Check admin panel at:
http://<EC2_PUBLIC_IP>:1337/admin

### 9️⃣ GitHub Push
# Switch remote to SSH
git remote set-url origin git@github.com:Noorjahan153/Task3_Intern.git

# Add, commit, and push
git add .
git commit -m "Add Strapi setup"
git push -u origin main

### 10️⃣ Notes / Common Issues
- For large projects, npm build can take several minutes on t3.medium
- Make sure port 1337 is open in EC2 Security Group
- If you see "Missing apiToken.salt", generate a 16-byte base64 and put in API_TOKEN_SALT
- For memory errors: export NODE_OPTIONS="--max-old-space-size=2048"
- If "port 1337 is already used", kill existing process:
  ps -ef | grep strapi
  kill -9 <pid>















