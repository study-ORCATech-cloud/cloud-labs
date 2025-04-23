# GCP LAB05: Resource Overview – Load Balancer and Managed Instance Group

## ⚖️ What Resources Are We Working With?

In **GCP LAB05**, you'll create a **Managed Instance Group (MIG)** backed by a startup script and front it with a **Global HTTP Load Balancer**. This lab teaches how to build scalable and highly available applications in GCP.

---

## 🧱 Managed Instance Group (MIG)

### Description
MIGs manage a group of identical VM instances, with auto-healing, autoscaling, and load balancing support.

### Key Concepts
- **Instance Template**: Defines the VM configuration
- **Autoscaling Policy**: Automatically adjusts VM count
- **Health Checks**: Ensure only healthy instances receive traffic

### What You Do in the Lab
- Create an instance template
- Deploy a MIG with startup script (e.g., install Apache)

---

## 🌐 HTTP(S) Load Balancer

### Description
Distributes traffic globally to backend services based on URL maps and health checks.

### Key Concepts
- **Frontend IP**: Global, static public IP
- **Backend Service**: Connects to MIGs
- **URL Map & Proxy**: Routes HTTP traffic

### What You Do in the Lab
- Create a global HTTP Load Balancer
- Attach it to a MIG with health checks

---

## 📈 Autoscaling

### Description
Automatically increases or decreases the number of VM instances based on metrics.

### What You Do in the Lab
- Define autoscaling policy based on CPU utilization
- Trigger scaling events with simulated load

---

## ✅ Summary

| Resource              | Purpose                                         | Created Using    |
|-----------------------|--------------------------------------------------|------------------|
| Managed Instance Group| Automates VM scaling and updates                | Console & CLI    |
| HTTP(S) Load Balancer | Routes global traffic to healthy backends       | Console & CLI    |
| Instance Template     | Blueprint for consistent VM creation            | Console & CLI    |

This lab demonstrates how to deploy resilient and scalable services using native GCP components.

