# LAB05: Deploy Scalable App with Load Balancer and Instance Group

## ⚖️ Lab Overview

In this lab, you’ll deploy a web server application using a **Managed Instance Group (MIG)** and distribute traffic using a **Global HTTP Load Balancer**. This lab demonstrates scalable infrastructure deployment in GCP.

---

## 🎯 Objectives

- Create an instance template with startup script
- Deploy a MIG across zones
- Set up HTTP load balancing
- Test high availability and scaling

---

## 🛠️ Prerequisites

- GCP project with billing enabled
- Basic familiarity with VM creation

---

## 🧱 Part 1: Create Instance Template

1. Go to **Compute Engine > Instance templates > Create instance template**
2. Name: `web-template`
3. Region: `us-central1`, Machine type: `e2-micro`
4. Startup script:
```bash
#! /bin/bash
apt update
apt install apache2 -y
echo "Hello from MIG" > /var/www/html/index.html
```
5. Save the template

---

## 🧩 Part 2: Create Managed Instance Group

1. Go to **Instance Groups > Create Instance Group**
2. Name: `web-mig`, Location: `Multiple zones in us-central1`
3. Use `web-template`
4. Size: 2 instances (initial)
5. Enable autoscaling: CPU target = 60%

---

## 🌐 Part 3: Configure HTTP Load Balancer

1. Go to **Network Services > Load Balancing > Create Load Balancer**
2. Type: **Start configuration > HTTP(S) Load Balancing > From Internet to my VMs**
3. Create:
   - Backend service: Add `web-mig` and health check (port 80)
   - URL map: Default route to backend
   - Frontend IP: Create new, global, HTTP port 80
4. Review and deploy

---

## 🔎 Part 4: Test the Load Balancer

1. Use the public IP of the load balancer
2. Refresh to verify responses from MIG instances

---

## 🧪 Challenge

- Trigger autoscaling by generating CPU load
- Create a second MIG in another region and add to backend service

---

## 🧹 Cleanup

- Delete the load balancer, instance group, template, and firewall rules

---

## ✅ Summary

You’ve successfully deployed a global, scalable web application using GCP’s managed infrastructure and load balancing services.