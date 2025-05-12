# AWS LAB01B: Resource Overview

## 🔧 What Resources Are We Working With?

In **AWS LAB01B**, we're focusing on **Amazon Elastic File System (EFS)** - AWS's fully managed elastic NFS file system that allows you to share file data across multiple EC2 instances and AWS services. This lab builds upon your EC2 knowledge and explores shared, scalable file storage.

---

## 📂 EFS File System

### Description
Amazon EFS provides serverless, fully elastic file storage that lets you share file data without provisioning or managing storage capacity and performance. EFS file systems can grow and shrink automatically as you add and remove files.

### Key Concepts
- **Elastic Storage**: Automatically scales as you add or remove files
- **Shared Access**: Multiple EC2 instances can access the same file system
- **Performance Modes**: General Purpose and Max I/O for different workloads
- **Throughput Modes**: Bursting and Provisioned options
- **Access Points**: Control access to specific application directories
- **Lifecycle Management**: Automatically transitions files to cost-effective storage tiers

### What You Do in the Lab
- Create an EFS file system
- Configure mount targets in multiple availability zones
- Set up security groups for EFS access
- Mount the file system on EC2 instances
- Test shared file access between instances
- Configure automatic mounting with /etc/fstab

---

## 🔒 Security and Access Control

### Description
EFS provides multiple levels of access control for your file systems.

### Key Concepts
- **Network Access**: Security groups controlling instance-to-EFS traffic
- **IAM Permissions**: Control who can administer the file system
- **EFS Access Points**: Application-specific entry points with user/permission settings
- **VPC Endpoints**: Access EFS without traversing the internet

### What You Do in the Lab
- Create security groups specific to EFS
- Configure network access controls
- Set up mount targets in multiple subnets
- Configure file-level permissions

---

## 🌐 Multi-AZ Deployment

### Description
Understanding how EFS provides high availability across multiple Availability Zones.

### Key Concepts
- **Mount Targets**: Network interfaces in each AZ to access your file system
- **High Availability**: Redundancy across multiple AZs
- **Shared State**: Same data available to all connected instances

### What You Do in the Lab
- Create mount targets in multiple AZs
- Launch EC2 instances in different AZs
- Verify file system access is consistent across all instances

---

## ✅ Summary

| Resource         | Purpose                                     | Created Using     |
|------------------|---------------------------------------------|--------------------|
| EFS File System  | Shared elastic storage                      | Console & CLI      |
| Mount Targets    | Access points in each Availability Zone     | Created            |
| Security Groups  | Control network access to the file system   | Created            |
| EC2 Instances    | Compute resources to access shared storage  | From LAB01         |
| NFS Mount        | Protocol for accessing the file system      | Configured         |

EFS is essential for applications that need shared storage across multiple instances or services. It provides a fully managed file system that scales automatically without the need to provision storage in advance! 