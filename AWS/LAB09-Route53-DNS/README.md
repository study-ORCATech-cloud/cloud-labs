# AWS LAB09: Resource Overview – Route 53 and Custom Domains

## 🌍 What Resources Are We Working With?

In **AWS LAB09**, you'll work with **Amazon Route 53**, a scalable DNS and domain registration service. You'll learn how to register or manage custom domains, configure DNS records, and route traffic to AWS services or external endpoints.

---

## 📡 Amazon Route 53

### Description
Route 53 is AWS's Domain Name System (DNS) web service that routes users to internet applications by translating friendly domain names (like `example.com`) into IP addresses.

### Key Concepts
- **Hosted Zone**: Container for DNS records for a domain
- **Record Set**: Defines how traffic is routed (e.g., A, CNAME, MX)
- **Routing Policies**: Simple, weighted, failover, latency-based, geolocation, etc.

### What You Do in the Lab
- Create or import a domain
- Create a hosted zone for your domain
- Add A and CNAME records
- Route traffic to an S3 static website or EC2 instance

---

## 🌐 Domain Registration (Optional)

### Description
Register a new domain directly in Route 53, or use an external registrar and point it to AWS name servers.

### What You Do in the Lab
- (Optional) Register a new domain
- Update name servers in registrar if using an external domain

---

## 🧭 DNS Record Types

### Description
DNS records define how queries for your domain are handled.

### What You Use in the Lab
- **A Record**: Maps domain to IP address (e.g., EC2 instance)
- **CNAME Record**: Aliases to another domain name (e.g., S3 website endpoint)

---

## ✅ Summary

| Resource       | Purpose                                            | Created Using    |
|----------------|-----------------------------------------------------|------------------|
| Route 53       | Domain name system service                         | Console          |
| Hosted Zone    | DNS zone for managing records                      | Console          |
| A / CNAME Record| Routes traffic to EC2 or S3                        | Console          |
| Custom Domain  | Enables friendly URLs and branded access           | Console (optional)|

This lab helps you connect infrastructure to the real world by using DNS to provide friendly, professional URLs for your apps.

