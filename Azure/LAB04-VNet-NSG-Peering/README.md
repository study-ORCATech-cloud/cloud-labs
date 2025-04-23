# Azure LAB04: Resource Overview – Virtual Networks, NSGs, and Subnet Peering

## 🌐 What Resources Are We Working With?

In **Azure LAB04**, you will explore core **Azure Networking** concepts by creating **Virtual Networks (VNets)**, configuring **Network Security Groups (NSGs)**, and establishing **subnet peering**. This lab lays the groundwork for securing and segmenting cloud workloads.

---

## 🧱 Virtual Network (VNet)

### Description
A VNet is a logically isolated network in Azure that enables communication between resources.

### Key Concepts
- **CIDR range**: Defines the IP address space (e.g., `10.0.0.0/16`)
- **Subnets**: Divide the VNet for organizational and security purposes
- **Regional scope**: VNets are created per region

### What You Do in the Lab
- Create a VNet with multiple subnets (public/private)

---

## 🔐 Network Security Group (NSG)

### Description
NSGs act as firewalls at the subnet or network interface level, controlling inbound and outbound traffic.

### Key Concepts
- **Rules**: Define protocol, port, source, and destination
- **Default rules**: Block all inbound and allow outbound
- **Priority**: Determines rule application order

### What You Do in the Lab
- Create an NSG
- Allow SSH (port 22) to public subnet
- Apply NSG to subnet or NIC

---

## 🔄 Subnet Peering (VNet Peering)

### Description
Enables private traffic routing between two VNets (or subnets) without going through the internet.

### Key Concepts
- **Bidirectional**: Peering must be enabled from both ends
- **Same/different subscriptions or regions**
- **No IP address overlap**

### What You Do in the Lab
- Peer two VNets
- Confirm communication between subnets

---

## ✅ Summary

| Resource             | Purpose                                           | Created Using    |
|----------------------|--------------------------------------------------|------------------|
| Virtual Network      | Defines an isolated cloud network                | Portal & CLI     |
| Subnets              | Segment the VNet for routing and isolation       | Portal & CLI     |
| Network Security Group| Enforces security rules for network traffic     | Portal & CLI     |
| VNet Peering         | Connects networks securely and privately         | Portal & CLI     |

This lab is essential for building secure, well-architected Azure infrastructure.

