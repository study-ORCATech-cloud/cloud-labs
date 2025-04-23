# LAB09: Map a Custom Domain with Azure DNS

## 🌍 Lab Overview

This lab walks you through mapping a **custom domain** to Azure resources using **Azure DNS**. You’ll create a DNS zone, add A and CNAME records, and optionally configure your registrar to delegate name resolution to Azure.

---

## 🎯 Objectives

- Create and manage DNS zones
- Add A and CNAME records
- Route domain names to web apps or storage endpoints
- Optionally delegate domain from external registrar

---

## 🛠️ Prerequisites

- Azure subscription
- A registered domain name (e.g., from Namecheap or GoDaddy)

---

## 🧱 Part 1: Create DNS Zone

1. Go to **Azure DNS Zones > Create**
2. Domain name: `examplelabs.com`
3. Resource group: `lab09-rg`
4. Region: Global
5. Create the zone

---

## 🔁 Part 2: Add DNS Records

1. Go to your DNS zone > **+ Record Set**
2. Add A Record:
   - Name: `@`
   - Type: A
   - IP Address: Public IP of a web app or load balancer
3. Add CNAME Record:
   - Name: `www`
   - Alias: `examplelabs.azurewebsites.net`

---

## 🌐 Part 3: External Registrar (Optional)

1. Copy Azure’s NS (Name Server) records from DNS Zone
2. Go to your domain registrar
3. Replace the domain’s name servers with the Azure-provided ones
4. Wait for propagation (may take a few hours)

---

## 💻 Optional CLI Workflow
```bash
az network dns zone create --name examplelabs.com --resource-group lab09-rg
az network dns record-set a add-record --zone-name examplelabs.com --resource-group lab09-rg --record-set-name @ --ipv4-address <your-ip>
```

---

## 🧪 Challenge

- Add TXT records for email verification (SPF, DKIM)
- Configure MX records for email hosting

---

## 🧹 Cleanup

- Delete the DNS zone and resource group

---

## ✅ Summary

This lab demonstrates how to integrate custom domains with Azure services using Azure DNS, an essential skill for branding and global availability of cloud-based applications.

