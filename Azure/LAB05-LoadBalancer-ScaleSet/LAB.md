# LAB05: Deploy Azure Load Balancer with Virtual Machine Scale Set

## ⚖️ Lab Overview

In this lab, you’ll create a **Virtual Machine Scale Set (VMSS)** that automatically adjusts the number of VM instances based on demand, and front it with an **Azure Load Balancer** to evenly distribute traffic.

---

## 🎯 Objectives

- Deploy a VMSS with startup configuration
- Create and configure an Azure Load Balancer
- Attach VMSS to the Load Balancer’s backend pool
- Test load balancing and scaling

---

## 🛠️ Prerequisites

- Azure subscription
- Azure CLI or Portal access

---

## 🧱 Part 1: Create VMSS with Custom Script

1. Go to **Virtual Machine Scale Sets > Create**
2. Name: `lab05-scale`
3. Region: Any (e.g., East US)
4. Image: Ubuntu LTS
5. Custom Script (install Nginx):
```bash
#!/bin/bash
apt-get update && apt-get install -y nginx
```
6. Instance count: Start with 2, Max: 5
7. Scaling Rule: CPU > 60% = scale out, < 30% = scale in

---

## 🌐 Part 2: Create Load Balancer and Connect

1. Go to **Load Balancers > Create**
2. Type: Public, Name: `lab05-lb`
3. Frontend IP: Create public IP
4. Backend Pool: Add VMSS
5. Health Probe: HTTP, port 80
6. Load Balancing Rule:
   - Port: 80 → 80
   - Backend pool: VMSS

---

## 🔁 Part 3: Test Load Balancer

1. Go to **Load Balancer > Frontend IP**
2. Access via browser: Should load from a VM instance
3. Refresh multiple times to test balancing behavior

---

## 💻 Optional CLI (Create VMSS & LB)
```bash
az vmss create --resource-group lab05-rg --name lab05-scale \
  --image UbuntuLTS --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt --admin-username azureuser --generate-ssh-keys
```

---

## 🧪 Challenge

- Configure diagnostic logs for VMSS
- Enable autoscaling for memory usage instead of CPU

---

## 🧹 Cleanup

- Delete the Load Balancer, Public IP, and VM Scale Set

---

## ✅ Summary

You’ve now built a resilient and scalable web infrastructure using Azure-native services—ideal for apps needing elastic capacity and balanced load distribution.

