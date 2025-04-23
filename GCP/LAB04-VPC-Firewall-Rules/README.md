# GCP LAB04: Resource Overview – VPC Networks and Firewall Rules

## 🌐 What Resources Are We Working With?

In **GCP LAB04**, you’ll build custom **Virtual Private Cloud (VPC)** networks and enforce security policies using **firewall rules**. This lab is foundational for understanding how network isolation and access control are implemented in Google Cloud.

---

## 🧱 VPC Network

### Description
A VPC is a logically isolated network that allows private IP communication between GCP resources.

### Key Concepts
- **Subnets**: IP ranges within regions
- **Auto vs. Custom Mode**: Auto creates default subnets; custom gives full control
- **Global Resource**: Unlike AWS/Azure, a VPC spans all regions

### What You Do in the Lab
- Create a custom-mode VPC with two subnets
- Associate one subnet with an internet gateway (default route)

---

## 🔥 Firewall Rules

### Description
Firewall rules in GCP control traffic to and from VM instances at the network level.

### Key Concepts
- **Allow vs. Deny Rules**
- **Target Tags**: Used to apply rules to specific instances
- **Priority**: Lower values mean higher precedence

### What You Do in the Lab
- Allow SSH and HTTP access to VMs using allow rules
- Use target tags to restrict which instances the rules apply to

---

## 📡 Internet Gateway (Implicit)

### Description
GCP automatically provides internet access through a default route and NAT if public IPs are used.

### What You Do in the Lab
- Use public IPs to allow access
- Confirm VMs can reach the internet

---

## ✅ Summary

| Resource         | Purpose                                        | Created Using    |
|------------------|------------------------------------------------|------------------|
| VPC Network      | Isolated virtual network for resource grouping| Console & CLI    |
| Subnets          | Regional segmentation of VPC                  | Console & CLI    |
| Firewall Rules   | Controls inbound and outbound traffic         | Console & CLI    |

This lab gives you hands-on experience building and securing private networks in GCP, preparing you for multi-tier application deployments.