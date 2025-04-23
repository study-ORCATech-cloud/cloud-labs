# GCP LAB09: Resource Overview – Cloud DNS and Custom Domains

## 🌐 What Resources Are We Working With?

In **GCP LAB09**, you’ll configure **Cloud DNS** to route traffic from custom domains to Google Cloud services. This lab helps you deliver applications using user-friendly URLs.

---

## 🧭 Cloud DNS Zone

### Description
Managed DNS service to publish domain name systems for your domains.

### Key Concepts
- **Zone Name**: Unique ID for managing records under a domain
- **DNSSEC**: Optional DNS security feature
- **Name Servers**: Provided by Google for delegation

### What You Do in the Lab
- Create a public DNS zone for `examplelab.com`
- Link domain to the zone

---

## 🪧 DNS Records

### Description
Define how requests to your domain are routed.

### What You Do in the Lab
- Add **A record**: Route to VM instance or load balancer IP
- Add **CNAME record**: Alias `www.examplelab.com` to primary service

---

## 🔗 Domain Delegation (External Registrar)

### Description
If your domain is registered elsewhere (e.g., Namecheap), update its nameservers to Google Cloud DNS.

### What You Do in the Lab
- Copy Google name servers from the DNS zone
- Update registrar settings

---

## ✅ Summary

| Resource        | Purpose                                           | Created Using    |
|------------------|----------------------------------------------------|------------------|
| DNS Zone         | Manage domain name records                        | Console & CLI    |
| A / CNAME Record | Route traffic to apps, services, websites         | Console & CLI    |
| Domain Delegation| Use Google DNS with domains from other registrars | Registrar Panel  |

This lab shows how to integrate custom domain names with GCP services for professional branding and user accessibility.

