# LAB04: Design a Secure Network with VNet, NSG, and Subnet Peering

## 🌐 Lab Overview

This lab introduces **core Azure networking** concepts by creating an isolated **Virtual Network**, adding **public and private subnets**, applying **Network Security Groups (NSGs)** for access control, and connecting multiple VNets using **VNet peering**.

---

## 🎯 Objectives

- Create a virtual network with multiple subnets
- Configure NSGs to limit access by port and IP
- Peer two virtual networks for private communication

---

## 🛠️ Prerequisites

- Azure account with contributor access
- Azure CLI or Portal access

---

## 🧱 Part 1: Create VNet and Subnets (Portal)

1. Go to **Virtual networks > Create**
2. Name: `lab04-vnet`
3. Address space: `10.1.0.0/16`
4. Add Subnet 1: `public-subnet` → `10.1.1.0/24`
5. Add Subnet 2: `private-subnet` → `10.1.2.0/24`

---

## 🔐 Part 2: Create and Apply NSGs

1. Go to **NSGs > Create NSG**: `public-nsg`
2. Add inbound rule:
   - Priority: 100
   - Source: My IP
   - Port: 22 (SSH)
   - Allow
3. Apply `public-nsg` to the public subnet
4. Create `private-nsg` (no inbound rules)

---

## 🔁 Part 3: Peer Two VNets

1. Create another VNet: `lab04-vnet-peer`, CIDR: `10.2.0.0/16`
2. Peer both VNets:
   - Go to `lab04-vnet` > Peerings > Add
   - Remote VNet: `lab04-vnet-peer`
   - Enable traffic in both directions

---

## 💻 Optional CLI Workflow
```bash
az network vnet create --name lab04-vnet --resource-group lab04-rg --address-prefix 10.1.0.0/16
az network vnet subnet create --vnet-name lab04-vnet --name public-subnet --address-prefix 10.1.1.0/24
az network nsg create --resource-group lab04-rg --name public-nsg
```

---

## 🧪 Challenge

- Deploy VMs in each subnet and test peering with ping or curl
- Restrict outbound internet from the private subnet

---

## 🧹 Cleanup

- Delete both VNets, NSGs, and associated subnets

---

## ✅ Summary

This lab gave you hands-on experience with Azure network isolation, security filtering, and peering—key for secure multi-tier deployments in production environments.