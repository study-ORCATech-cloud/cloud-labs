# AWS LAB01A: Resource Overview

## 🔧 What Resources Are We Working With?

In **AWS LAB01A**, we're focusing on **Amazon Elastic Block Store (EBS)** - AWS's block storage service designed to be used with EC2 instances. This lab builds upon your EC2 knowledge and explores how to create, attach, and manage persistent storage volumes.

---

## 💾 EBS Volumes

### Description
Amazon EBS provides persistent block-level storage volumes for use with Amazon EC2 instances. EBS volumes are network-attached storage that persist independently from the life of an instance.

### Key Concepts
- **Volume Types**: Different types for different workloads (gp3, io2, st1, sc1)
- **IOPS & Throughput**: Performance metrics for storage
- **Snapshots**: Point-in-time backups of volumes
- **Encryption**: Data-at-rest protection for sensitive information
- **Volume Lifecycle**: Create, attach, detach, delete operations

### What You Do in the Lab
- Create EBS volumes of different types
- Attach volumes to EC2 instances
- Format and mount volumes
- Create snapshots for backup
- Restore volumes from snapshots
- Resize volumes to accommodate data growth

---

## 📁 File Systems

### Description
Once an EBS volume is attached to an instance, you need to create a file system on it before you can store data.

### Key Concepts
- **File System Types**: ext4, xfs, etc.
- **Mounting**: Attaching the volume to a specific directory
- **fstab**: Configuration for automatically mounting volumes

### What You Do in the Lab
- Format EBS volumes with file systems
- Mount volumes to directory paths
- Configure persistent mounts
- Manage data on the volumes

---

## 🌩️ EC2 Integration

### Description
Understanding how EBS volumes interact with EC2 instances.

### Key Concepts
- **Root Volumes**: Boot volumes for EC2 instances
- **Data Volumes**: Additional volumes for application data
- **Hot-Attach**: Adding volumes to running instances
- **Device Names**: How volumes appear to the OS (e.g., /dev/xvdf)

### What You Do in the Lab
- Attach volumes to running instances
- Configure multi-volume setups
- Ensure volume persistence across instance restarts

---

## ✅ Summary

| Resource         | Purpose                                    | Created Using     |
|------------------|--------------------------------------------|--------------------|
| EBS Volume       | Persistent block storage                   | Console & CLI      |
| Volume Types     | Match storage to workload requirements     | Selected           |
| Snapshots        | Back up data at a point in time            | Created            |
| Mount Points     | Access storage from within the instance    | Configured         |
| EC2 Instance     | Compute resource that uses the EBS volumes | From LAB01         |

These storage concepts are fundamental to building reliable applications in AWS. EBS provides the persistent layer where your critical data lives, even when instances are terminated! 