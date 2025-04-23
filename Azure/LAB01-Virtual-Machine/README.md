# Azure LAB01: Resource Overview

## 🔧 What Resources Are We Working With?

In **Azure LAB01**, we're working with **Virtual Machines**—one of the most commonly used services in Azure's IaaS (Infrastructure-as-a-Service) offerings. This lab helps you understand how to provision a VM and how the associated resources interact with each other.

---

## 🛠️ Virtual Machine (VM)

### Description
An Azure Virtual Machine provides on-demand, scalable computing resources. It works similarly to a physical computer, allowing you to run applications, scripts, and services.

### Key Concepts
- **Image**: Pre-configured OS like Ubuntu, Windows Server
- **Size**: Defines the CPU, memory, and cost (e.g., `Standard_B1s` for free tier)
- **Authentication**: SSH key or password-based login
- **OS Disk**: The root disk with the operating system installed

### What You Do in the Lab
- Launch a VM using Azure Portal and Azure CLI
- Choose an image and size
- Configure login access (SSH key)
- Access the VM via SSH

---

## 🏢 Resource Group

### Description
A resource group is a logical container for Azure resources. All resources in a group share the same lifecycle (create, update, delete).

### What You Do in the Lab
- Create a resource group to host the VM and networking resources

---

## 🔌 Virtual Network & Subnet (Implicit)

### Description
Azure VMs are deployed into a **Virtual Network (VNet)**, which includes one or more **Subnets** for IP address management.

### What You Do in the Lab
- Use the default VNet/subnet when creating a VM (optional to customize at this stage)

---

## 🚧 Network Security Group (NSG)

### Description
NSGs control inbound and outbound traffic to your VM—similar to a firewall.

### Key Concepts
- **Inbound Rules**: Define allowed incoming traffic (e.g., SSH on port 22)
- **Outbound Rules**: Define allowed outgoing traffic (usually open by default)

### What You Do in the Lab
- Configure NSG rules to allow SSH traffic
- Optionally allow HTTP traffic for web server challenge

---

## 🔺 Public IP Address

### Description
Azure VMs use dynamic or static public IP addresses to allow external access from the internet.

### What You Do in the Lab
- Use the assigned public IP to SSH into the VM

---

## 🚀 Summary

| Resource              | Purpose                                 | Created Using     |
|-----------------------|------------------------------------------|-------------------|
| Virtual Machine       | Host apps, scripts, or services          | Portal & CLI      |
| Resource Group        | Logical container for managing resources | Portal & CLI      |
| Image & Size          | Determine OS and machine specs           | Selected during VM setup |
| Network Security Group| Controls access to/from VM               | Portal & CLI      |
| Public IP Address     | Enables SSH and web access               | Auto-assigned     |
| Virtual Network       | Network layer for VM communication       | Auto-generated    |

This foundational lab sets the stage for advanced scenarios involving automation, scaling, and multi-tier architectures in Azure.