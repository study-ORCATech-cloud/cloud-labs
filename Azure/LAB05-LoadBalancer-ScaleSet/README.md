# Azure LAB05: Resource Overview – Load Balancer and Virtual Machine Scale Set

## ⚖️ What Resources Are We Working With?

In **Azure LAB05**, you’ll build a highly available compute environment using an **Azure Load Balancer** and a **Virtual Machine Scale Set (VMSS)**. These services help distribute traffic and dynamically scale application instances based on load.

---

## 🌐 Azure Load Balancer (Standard)

### Description
Distributes incoming network traffic across multiple backend VMs.

### Key Concepts
- **Frontend IP**: Public IP for accepting requests
- **Backend Pool**: Targets to forward traffic to (VMs or VMSS)
- **Health Probes**: Monitor instance availability
- **Load Balancing Rules**: Define traffic direction and port mapping

### What You Do in the Lab
- Create a Load Balancer
- Attach to VMSS backend pool
- Set up health probe and load rule (e.g., port 80)

---

## 📈 Virtual Machine Scale Set (VMSS)

### Description
A VMSS automatically manages identical VM instances for autoscaling and high availability.

### Key Concepts
- **Custom Script Extension**: Used for bootstrapping web server
- **Scaling Rules**: Based on CPU, schedule, or instance count
- **Instance Distribution**: Across AZs if needed

### What You Do in the Lab
- Create a VMSS with a Linux image
- Use cloud-init or shell script to install Apache/Nginx
- Connect to backend pool of Load Balancer

---

## 📦 Public IP Address

### Description
Used to expose the Load Balancer endpoint to the internet.

### What You Do in the Lab
- Assign static public IP to the Load Balancer frontend

---

## ✅ Summary

| Resource                  | Purpose                                            | Created Using    |
|---------------------------|---------------------------------------------------|------------------|
| Load Balancer             | Routes traffic to backend VMs                     | Portal & CLI     |
| VM Scale Set              | Autoscaling compute instances                     | Portal & CLI     |
| Public IP Address         | Provides external access to Load Balancer         | Portal & CLI     |

This lab teaches you how to build highly available infrastructure in Azure, a core requirement for production-grade deployments.

