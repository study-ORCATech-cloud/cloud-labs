# LAB04: Build a Secure Network with VPC and Firewall Rules

## 🌐 Lab Overview

In this lab, you will create a custom **Virtual Private Cloud (VPC)** network and configure **firewall rules** to control inbound and outbound access. This is fundamental for securing your workloads in Google Cloud.

---

## 🎯 Objectives

- Create a custom-mode VPC with subnets
- Define firewall rules for SSH and HTTP access
- Deploy a test VM to verify connectivity

---

## 🛠️ Prerequisites

- GCP project and access to Cloud Console or gcloud CLI
- Billing enabled

---

## 📋 Part 1: Create VPC and Subnets

1. Go to **VPC network > VPC networks > Create VPC network**
2. Name: `lab04-vpc`, Mode: **Custom**
3. Add Subnet 1:
   - Name: `public-subnet`, Region: `us-central1`, IP range: `10.0.1.0/24`
4. Add Subnet 2:
   - Name: `private-subnet`, Region: `us-central1`, IP range: `10.0.2.0/24`

---

## 🔐 Part 2: Configure Firewall Rules

1. Go to **VPC network > Firewall rules > Create Firewall Rule**
2. Rule name: `allow-ssh`
   - Targets: All instances in network
   - Source IP ranges: `0.0.0.0/0`
   - Protocols and ports: **Allow TCP:22**

3. Create a second rule: `allow-http`
   - Allow TCP:80 from anywhere

---

## 💻 Part 3: Deploy Test VM and Verify Access

1. Go to **Compute Engine > VM instances > Create Instance**
2. Name: `lab04-vm`
3. Network: `lab04-vpc`, Subnet: `public-subnet`
4. Firewall: Allow both HTTP and SSH traffic
5. SSH into VM and install web server (optional):
```bash
sudo apt update
sudo apt install apache2 -y
```

---

## 🧪 Challenge

- Create a firewall rule to **deny all ICMP traffic**
- Add a **target tag** to apply the rule to a specific VM only

---

## 🧹 Cleanup

- Delete the VM, firewall rules, and VPC network

---

## ✅ Summary

You've now created a custom network in GCP and applied firewall rules, preparing you to manage isolated and secure networking for your workloads.