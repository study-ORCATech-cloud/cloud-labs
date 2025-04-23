# AWS LAB01: Resource Overview

## 🔧 What Resources Are We Working With?

In **AWS LAB01**, we're working with one of the most fundamental cloud services: **Amazon EC2 (Elastic Compute Cloud)**. This lab introduces a few essential AWS components that every DevOps engineer should understand. Here's a breakdown:

---

## ✨ EC2 Instance (Virtual Machine)

### Description
Amazon EC2 provides scalable compute capacity in the cloud. It allows users to run virtual servers on demand.

### Key Concepts
- **AMI (Amazon Machine Image)**: A pre-configured OS template (e.g., Amazon Linux 2)
- **Instance Type**: Determines hardware specs (e.g., `t2.micro` for free tier)
- **Key Pair**: SSH keys for secure access to your instance
- **Security Group**: Acts as a virtual firewall to control inbound and outbound traffic
- **Elastic IP (optional)**: Static IP for long-term public access

### What You Do in the Lab
- Launch an EC2 instance
- Choose an AMI and instance type
- Assign a security group
- Generate/assign a key pair
- SSH into your virtual machine to explore it

---

## 🛡️ Security Group

### Description
Security Groups control network traffic to and from EC2 instances.

### Key Concepts
- **Inbound Rules**: Define what traffic is allowed to reach your instance (e.g., SSH on port 22)
- **Outbound Rules**: Define what traffic can leave your instance
- **Default Behavior**: All inbound traffic is denied unless explicitly allowed

### What You Do in the Lab
- Create a new security group
- Allow SSH access from your IP address
- Optionally allow HTTP access for testing web servers

---

## 👤 IAM (Implicit Use)

### Description
IAM (Identity and Access Management) is used to manage access to AWS services.

### Key Concepts
- You use an IAM user to run AWS CLI commands
- The IAM user must have sufficient permissions (e.g., `AmazonEC2FullAccess`)

### What You Do in the Lab
- Use IAM credentials configured with `aws configure` for CLI tasks

---

## ✅ Summary

| Resource         | Purpose                                | Created Using |
|------------------|----------------------------------------|----------------|
| EC2 Instance     | Runs your virtual server               | Console & CLI  |
| AMI              | Defines OS and base software           | Selected       |
| Instance Type    | Defines hardware spec and cost         | Selected       |
| Key Pair         | Enables secure SSH access              | Created/used   |
| Security Group   | Controls network traffic               | Created        |
| IAM User         | Authenticates CLI actions              | Pre-setup      |

These resources are foundational to almost every AWS use case. Gaining hands-on experience with them is a critical first step on your cloud journey!

