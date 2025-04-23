# GCP LAB01: Resource Overview

## 🔧 What Resources Are We Working With?

In **GCP LAB01**, we're using **Google Compute Engine**, which provides virtual machines on Google Cloud's infrastructure. You'll explore how to provision, access, and manage a VM along with its supporting resources.

---

## 🛠️ Compute Engine (VM Instance)

### Description
Compute Engine lets you create and run virtual machines. It’s one of the most essential services for developers, sysadmins, and DevOps engineers on GCP.

### Key Concepts
- **Machine Type**: Defines vCPU, memory, and cost (e.g., `e2-micro` for free tier)
- **Image**: OS template used to boot the VM (e.g., Ubuntu 20.04 LTS)
- **Boot Disk**: The root volume attached to the instance
- **Tags**: Used for firewall rules and network policies

### What You Do in the Lab
- Create a VM instance using both Console and CLI
- Choose a machine type and image
- Access your VM via SSH
- Explore default settings like startup scripts and service accounts

---

## 🏢 Project (Required for All GCP Resources)

### Description
Projects act as containers for all GCP resources. Every resource must be associated with a project.

### What You Do in the Lab
- Use an existing GCP project or create a new one
- Set it as the active project in the `gcloud` CLI

---

## 🌐 VPC Network and Subnet (Auto-created or Default)

### Description
Each VM instance lives inside a **Virtual Private Cloud (VPC)** network. GCP provides a **default VPC** with predefined subnets across regions.

### What You Do in the Lab
- Use the default VPC and subnet unless custom settings are required

---

## 🚧 Firewall Rules

### Description
Firewall rules control traffic to and from VM instances based on IP address, protocol, and port.

### Key Concepts
- GCP uses **network tags** on VMs to apply firewall rules
- **Allow HTTP/HTTPS** options apply rules automatically

### What You Do in the Lab
- Open SSH (port 22) access
- (Optional Challenge) Open port 80 for web server access using tags and rules

---

## 🛋️ External IP Address

### Description
Each VM can be assigned an ephemeral (temporary) or static external IP address for public access.

### What You Do in the Lab
- Retrieve the external IP and use it to SSH or access hosted content

---

## ✅ Summary

| Resource            | Purpose                                   | Created Using    |
|---------------------|--------------------------------------------|------------------|
| Compute Engine VM   | Provides a virtual server                  | Console & CLI    |
| Project             | Logical grouping of GCP resources          | Console & CLI    |
| VPC & Subnet        | Network infrastructure for the VM          | Default or custom|
| Firewall Rules      | Control traffic (SSH, HTTP, etc.)          | Auto/Custom      |
| External IP         | Enables public access to the VM            | Auto-assigned    |

This lab builds a solid understanding of how Compute Engine instances work within GCP and introduces critical concepts like networking, projects, and permissions—all essential for cloud practitioners.