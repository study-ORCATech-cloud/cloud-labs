# LAB04: Create a VPC with Subnets and Security Groups (AWS Console & CLI)

## 🌐 Lab Overview

In this lab, you will design and deploy a **custom Virtual Private Cloud (VPC)** in AWS. You’ll learn how to segment a network using **public and private subnets**, control access with **security groups**, and enable internet connectivity with a **route table** and **Internet Gateway**. This is a key skill for building secure and scalable cloud architectures.

---

## 🎯 Objectives

- Create a custom VPC with CIDR block
- Create public and private subnets in different Availability Zones
- Launch an EC2 instance into the public subnet
- Configure a security group to allow SSH access
- Attach an Internet Gateway and configure route tables

---

## 🛠️ Prerequisites

- AWS account
- AWS CLI installed and configured
- IAM permissions for VPC, EC2, and networking components

---

## 🖥️ Part 1: VPC and Subnets (Console)

1. **Navigate to VPC Dashboard** > Click **Create VPC**
2. **Set Name**: `lab04-vpc`
3. **CIDR Block**: `10.0.0.0/16`
4. Create the VPC

### Create Subnets
- **Public Subnet**: `10.0.1.0/24`, AZ `us-east-1a`, name: `lab04-public-subnet`
- **Private Subnet**: `10.0.2.0/24`, AZ `us-east-1b`, name: `lab04-private-subnet`

### Attach Internet Gateway
1. Create and name it `lab04-igw`
2. Attach to `lab04-vpc`

### Configure Route Table
1. Create a route table: `lab04-public-rt`
2. Add route: Destination `0.0.0.0/0`, Target: `lab04-igw`
3. Associate it with the **public subnet** only

---

## 🔐 Part 2: Security Group and EC2 (Console)

1. Create a **Security Group**: `lab04-sg`
2. Inbound rules:
   - Allow **SSH (port 22)** from your IP
3. Launch an EC2 instance:
   - Network: `lab04-vpc`, Subnet: `lab04-public-subnet`
   - Use the `lab04-sg` security group
   - Assign public IP and key pair

---

## 💻 Part 3: VPC via AWS CLI

```bash
# Create VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16

# Create Subnets
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.1.0/24 --availability-zone us-east-1a
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.2.0/24 --availability-zone us-east-1b

# Create and attach IGW
aws ec2 create-internet-gateway
aws ec2 attach-internet-gateway --internet-gateway-id <igw-id> --vpc-id <vpc-id>

# Create route table and associate
aws ec2 create-route-table --vpc-id <vpc-id>
aws ec2 create-route --route-table-id <rt-id> --destination-cidr-block 0.0.0.0/0 --gateway-id <igw-id>
aws ec2 associate-route-table --subnet-id <subnet-id> --route-table-id <rt-id>
```

---

## 🧪 Optional Challenge

- Create a NAT Gateway in the public subnet
- Allow private subnet instances to access the internet without public IPs

---

## 🧹 Cleanup

To avoid charges:
- Terminate EC2 instances
- Delete subnets, route tables, IGW, and the VPC

---

## ✅ Summary

In this lab, you:
- Built a custom VPC with public/private subnets
- Secured it with security groups
- Enabled external connectivity with an IGW and route table

This is essential groundwork for advanced networking and production-ready cloud design!

