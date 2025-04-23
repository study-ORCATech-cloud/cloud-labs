# LAB01: Launch an EC2 Instance (AWS Console & CLI)

## 🧠 Lab Overview

Welcome to your first AWS hands-on lab! In this exercise, you will learn how to create a virtual server (EC2 instance) on AWS. You will perform the same operation using both the **AWS Management Console (UI)** and the **AWS Command Line Interface (CLI)**. This dual approach ensures you get comfortable with both manual and script-based cloud operations.

## 🌟 Why EC2?
Amazon EC2 (Elastic Compute Cloud) is a foundational AWS service that lets you rent virtual servers on demand. It's commonly used to host websites, run applications, or perform general-purpose computing tasks.

---

## 🎯 Objectives

By the end of this lab, you will be able to:

- Understand what EC2 is and why it's important
- Launch an EC2 instance using the AWS Console
- Launch an EC2 instance using the AWS CLI
- Understand key EC2 configuration components (AMI, instance type, key pair, security group)
- Connect to the instance using SSH
- Clean up by terminating the instance

---

## 🛠️ Prerequisites

- AWS account (sign up at https://aws.amazon.com/free if you don't have one)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) installed and configured (`aws configure`)
- A key pair for SSH access (you can create one during the lab)
- Basic terminal/SSH knowledge recommended

---

## 📝 Part 1: Launch EC2 Using the AWS Console (UI)

1. **Login to AWS Console**: Visit [https://console.aws.amazon.com](https://console.aws.amazon.com) and log in.

2. **Navigate to EC2**:
   - Type "EC2" in the search bar and click the EC2 service.
   - Click "Instances" > "Launch Instance".

3. **Configure Your Instance**:
   - **Name**: `lab01-ui-instance`
   - **AMI**: Amazon Linux 2 AMI (free tier eligible)
   - **Instance Type**: `t2.micro` (free tier)
   - **Key Pair**: Create a new key pair or use an existing one
     - Save the `.pem` file securely!
   - **Network Settings**:
     - Click "Edit" under "Network settings"
     - Create a new security group
     - Allow SSH (port 22) from your IP address

4. **Launch Instance**:
   - Review and click "Launch Instance"

5. **Access the Instance**:
   - After a few moments, your instance will show as "Running"
   - Copy the **public IP address** from the instance details
   - SSH using:
     ```bash
     ssh -i path/to/your-key.pem ec2-user@<PUBLIC_IP>
     ```

6. **Explore the Instance**:
   ```bash
   uname -a
   top
   curl https://example.com
   ```

---

## 💻 Part 2: Launch EC2 Using AWS CLI

1. **Create a Security Group**:
   ```bash
   aws ec2 create-security-group \
     --group-name lab01-cli-sg \
     --description "Security group for CLI lab"
   ```

2. **Allow SSH Access**:
   Replace `YOUR_PUBLIC_IP` with your actual IP (use https://whatismyipaddress.com):
   ```bash
   aws ec2 authorize-security-group-ingress \
     --group-name lab01-cli-sg \
     --protocol tcp --port 22 \
     --cidr YOUR_PUBLIC_IP/32
   ```

3. **Get Latest Amazon Linux 2 AMI ID**:
   ```bash
   aws ssm get-parameters-by-path \
     --path "/aws/service/ami-amazon-linux-latest" \
     --query "Parameters[?Name=='/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2'].Value" \
     --output text
   ```

4. **Launch EC2 Instance**:
   Replace `ami-xxxxxxxx`, `your-key-name` with your values:
   ```bash
   aws ec2 run-instances \
     --image-id ami-xxxxxxxxxxxxxxxxx \
     --count 1 \
     --instance-type t2.micro \
     --key-name your-key-name \
     --security-groups lab01-cli-sg
   ```

5. **Get Public IP**:
   ```bash
   aws ec2 describe-instances \
     --query "Reservations[*].Instances[*].PublicIpAddress" \
     --output text
   ```

6. **Connect Using SSH**:
   ```bash
   ssh -i path/to/your-key.pem ec2-user@<PUBLIC_IP>
   ```

---

## 🧪 Optional Challenge

Install and host a basic web page:

```bash
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
echo "<h1>Hello from EC2!</h1>" | sudo tee /var/www/html/index.html
```

Then, add port 80 to your security group:
```bash
aws ec2 authorize-security-group-ingress \
  --group-name lab01-cli-sg \
  --protocol tcp --port 80 \
  --cidr 0.0.0.0/0
```

Open your EC2 public IP in a browser to view the webpage.

---

## 🌀 Cleanup

Don't forget to terminate your instance:
```bash
aws ec2 terminate-instances --instance-ids i-xxxxxxxxxxxxxxxxx
```

---

## ✅ Summary

You now know how to launch, access, and manage EC2 instances both via the AWS Console and the CLI. This is a fundamental skill that will help you in nearly every AWS project going forward. Great job!

---

Happy Clouding ☁️!

