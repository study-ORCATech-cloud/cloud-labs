# AWS LAB04: Resource Overview – VPC, Subnets, and Security Groups

## 🌐 What Resources Are We Working With?

In **AWS LAB04**, you’ll dive into the foundation of cloud networking by working with **Amazon Virtual Private Cloud (VPC)**. You'll create isolated network environments, define IP ranges, and control access with **subnets** and **security groups**.

This lab focuses on building secure, segmented networks that support application deployment and internal communication.

---

## 🌍 Virtual Private Cloud (VPC)

### Description
A VPC is your own private network within AWS. It enables you to launch AWS resources in a logically isolated section of the AWS Cloud.

### Key Concepts
- **CIDR Block**: IP address range for your network (e.g., `10.0.0.0/16`)
- **Default vs Custom VPC**: This lab uses a **custom VPC** for better control
- **Region-bound**: VPCs exist in one AWS region

### What You Do in the Lab
- Create a custom VPC with a specified IP range

---

## 🛤️ Subnets

### Description
Subnets divide your VPC into smaller networks. They define where your resources are placed within the VPC.

### Key Concepts
- **Public Subnet**: Has internet access via an Internet Gateway
- **Private Subnet**: Internal use only, no direct internet access
- **Availability Zones (AZs)**: Subnets are created within specific AZs

### What You Do in the Lab
- Create both public and private subnets
- Associate them with different AZs

---

## 🔐 Security Groups

### Description
Security Groups act as virtual firewalls for instances to control inbound and outbound traffic.

### Key Concepts
- Stateful: return traffic is automatically allowed
- Can be attached to EC2 instances or other AWS services
- Multiple SGs can be applied to a single instance

### What You Do in the Lab
- Create custom security groups
- Define inbound rules for SSH and HTTP
- Attach SGs to test EC2 instances

---

## 🌉 Internet Gateway (IGW)

### Description
An IGW enables internet access for public subnets within your VPC.

### What You Do in the Lab
- Create and attach an Internet Gateway to your VPC
- Update route tables to allow internet-bound traffic

---

## 🗺️ Route Table

### Description
Route tables define how network traffic is directed.

### What You Do in the Lab
- Create route tables for public and private subnets
- Associate public route table with IGW

---

## ✅ Summary

| Resource         | Purpose                                     | Created Using    |
|------------------|----------------------------------------------|------------------|
| VPC              | Isolated virtual network                    | Console & CLI    |
| Subnets          | Network segmentation                        | Console & CLI    |
| Security Groups  | Firewall to control traffic                 | Console & CLI    |
| Internet Gateway | Enables internet access for VPC             | Console & CLI    |
| Route Table      | Manages routing between subnets and internet| Console & CLI    |

Networking is the backbone of cloud architecture. Understanding how to segment, secure, and route traffic within a VPC prepares you for real-world deployments and production environments.