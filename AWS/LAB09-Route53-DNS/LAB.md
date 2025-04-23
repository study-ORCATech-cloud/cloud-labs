# LAB09: Configure Route 53 with a Custom Domain (Console & CLI)

## 🌍 Lab Overview

In this lab, you'll use **Amazon Route 53** to set up domain name resolution for AWS resources like S3-hosted websites or EC2 instances. You’ll register a domain or use an existing one, configure **DNS records**, and link your services to human-readable domain names.

---

## 🎯 Objectives

- Register or import a domain
- Create a hosted zone
- Add DNS records (A and CNAME)
- Route traffic to an EC2 instance or S3 bucket

---

## 🛠️ Prerequisites

- AWS account
- Public EC2 instance or S3 static site (from previous labs)
- Optional: Registered domain name

---

## 🖥️ Part 1: Register or Import a Domain

1. Go to **Route 53 > Domains** > Register Domain
2. Choose and register a domain (charges apply) **or**
3. Use an existing domain and update your registrar’s name servers later

---

## 📦 Part 2: Create Hosted Zone

1. Go to **Route 53 > Hosted zones > Create hosted zone**
2. Domain name: `example.com`
3. Type: **Public hosted zone**
4. AWS will create default NS and SOA records

---

## 🌐 Part 3: Add DNS Records

### Option A: Point to S3 Static Site
1. Upload and configure your S3 bucket for static site hosting
2. Add a **CNAME record**:
   - Name: `www`
   - Value: S3 endpoint (e.g., `labsite.s3-website-us-east-1.amazonaws.com`)

### Option B: Point to EC2 Instance
1. Get the **public IPv4 address** of your EC2 instance
2. Add an **A record**:
   - Name: `@` (for root domain)
   - Value: IP address of EC2 instance

---

## 💻 Part 4: CLI Sample for Record Creation
```bash
aws route53 change-resource-record-sets \
  --hosted-zone-id <zone-id> \
  --change-batch ' {
    "Changes": [
      {
        "Action": "UPSERT",
        "ResourceRecordSet": {
          "Name": "example.com.",
          "Type": "A",
          "TTL": 300,
          "ResourceRecords": [
            { "Value": "203.0.113.25" }
          ]
        }
      }
    ]
  }'
```

---

## 🧪 Challenge

- Add a subdomain (e.g., `blog.example.com`) that points to a different service
- Set up MX records for email integration

---

## 🧹 Cleanup

- Delete the hosted zone
- Release the domain (optional, if registered via AWS)

---

## ✅ Summary

You've configured a DNS zone in AWS and linked a domain to cloud resources. This is key to making applications accessible and production-ready with branded URLs.