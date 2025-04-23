# Azure LAB09: Resource Overview – Azure DNS and Custom Domain

## 🌍 What Resources Are We Working With?

In **Azure LAB09**, you'll map a **custom domain name** to Azure resources using **Azure DNS**. This includes creating DNS zones, records (A, CNAME), and optionally pointing domains from external registrars to Azure name servers.

---

## 🌐 Azure DNS Zone

### Description
An Azure DNS zone hosts the DNS records for a domain, allowing you to manage records programmatically or via the portal.

### Key Concepts
- **DNS Zone Name**: Typically matches your domain (e.g., `example.com`)
- **Name Servers**: Provided by Azure for domain delegation
- **SOA/NS Records**: Created automatically

### What You Do in the Lab
- Create a DNS zone for your custom domain
- Delegate your domain to Azure if registered externally

---

## 🔁 DNS Records

### Description
Records define how DNS queries are resolved for your domain.

### Common Types Used
- **A Record**: Maps domain to IP (e.g., EC2 or App Service)
- **CNAME**: Aliases one domain to another (e.g., Azure Blob endpoint)

### What You Do in the Lab
- Create an A record for root domain
- Create CNAME for subdomain (e.g., `www`) to point to hosted service

---

## 🧩 Optional: External Registrar Integration

### Description
If your domain is registered outside Azure, update its nameservers to those from Azure DNS.

### What You Do in the Lab
- Copy Azure DNS NS records
- Update registrar settings to use Azure name servers

---

## ✅ Summary

| Resource           | Purpose                                         | Created Using    |
|--------------------|--------------------------------------------------|------------------|
| Azure DNS Zone     | Hosts DNS records for your domain               | Portal & CLI     |
| A / CNAME Records  | Define how traffic is routed for subdomains     | Portal & CLI     |
| Nameserver Update  | Delegates external domains to Azure DNS         | Registrar Panel  |

This lab teaches you how to build user-friendly URLs and ensure availability by integrating DNS with your Azure services.

