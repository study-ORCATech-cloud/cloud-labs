# LAB09: Map Custom Domain to GCP Services with Cloud DNS

## 🌍 Lab Overview

In this lab, you will configure **Cloud DNS** to map a custom domain to a public IP or service hosted in GCP. This enables human-friendly URLs for applications.

---

## 🎯 Objectives

- Create a Cloud DNS zone for your domain
- Add DNS records (A, CNAME)
- Delegate domain to Google name servers

---

## 🛠️ Prerequisites

- A registered domain (e.g., from Namecheap, GoDaddy)
- GCP project with billing enabled
- Web app or IP to point to (e.g., Load Balancer or VM)

---

## 📋 Part 1: Create Public DNS Zone

1. Go to **Cloud DNS > Create Zone**
2. Name: `examplelab-zone`
3. DNS name: `examplelab.com`
4. Type: **Public**
5. Save and note name servers provided by Google

---

## 🪧 Part 2: Add DNS Records

1. In your zone, click **Add Record Set**
2. Create A record:
   - Name: `@`
   - Type: A
   - TTL: 300
   - IP address: Public IP of VM or load balancer

3. Create CNAME record:
   - Name: `www`
   - Type: CNAME
   - TTL: 300
   - Canonical name: `@` (or use App Engine/Cloud Run domain)

---

## 🔗 Part 3: Update Domain Registrar

1. Copy the **NS (Name Server)** entries from Cloud DNS
2. Log into your domain registrar dashboard
3. Replace existing name servers with the ones from Google
4. Wait for propagation (up to 48 hours, usually faster)

---

## 🧪 Challenge

- Add a TXT record for domain verification (e.g., Google Workspace)
- Add MX records to enable email hosting (e.g., Gmail)

---

## 🧹 Cleanup

- Remove DNS zone and any deployed services (e.g., VMs)

---

## ✅ Summary

You’ve now set up custom domain routing in GCP using Cloud DNS. This is a critical skill for real-world deployments, allowing services to be accessed using professional URLs.

