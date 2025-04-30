# AWS LAB14: Resource Overview – Amazon CloudFront and AWS WAF

## 🗄️ What Resources Are We Working With?

In **AWS LAB14**, you'll explore **content delivery and web security** using **Amazon CloudFront and AWS WAF**. This lab teaches you how to set up a global content delivery network (CDN) to deliver content with low latency and high performance, while implementing security controls to protect your applications from common web exploits and bots.

---

## 📊 Amazon CloudFront

### Description
CloudFront is a fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally with low latency and high transfer speeds, all within a developer-friendly environment.

### Key Concepts
- **Distributions**: The basic CloudFront entity that controls how content is delivered
- **Origins**: Source locations for your content (e.g., S3 buckets, EC2 instances, or custom HTTP servers)
- **Cache Behaviors**: Rules that determine how CloudFront handles requests for different content types
- **Edge Locations**: Global network of data centers where content is cached for low-latency delivery
- **Regional Edge Caches**: Larger caches that sit between edge locations and origins
- **TTL (Time To Live)**: Controls how long content stays cached at edge locations

### What You Do in the Lab
- Create and configure a CloudFront distribution
- Set up an S3 bucket as an origin
- Configure cache behaviors and TTL settings
- Set up origin access identity (OAI) for secure S3 access
- Customize error responses and header handling
- Monitor CloudFront metrics and logs

---

## 🔎 AWS WAF

### Description
AWS WAF (Web Application Firewall) helps protect your web applications from common web exploits and bots that could affect application availability, compromise security, or consume excessive resources.

### Key Concepts
- **Web ACL (Access Control List)**: Main component defining rules for filtering requests
- **Rules**: Conditions that inspect different parts of HTTP requests
- **Rule Groups**: Reusable sets of rules that can be shared across web ACLs
- **IP Sets**: Collections of IP addresses and ranges for blocking or allowing
- **Rate-Based Rules**: Rules that count requests and block IPs that exceed thresholds
- **Managed Rules**: Pre-configured rule sets maintained by AWS or AWS Marketplace sellers

### What You Do in the Lab
- Create and configure a AWS WAF web ACL
- Set up rule groups with different security controls
- Implement rate limiting to prevent brute force attacks
- Associate WAF with your CloudFront distribution
- Configure AWS managed rule sets for common vulnerabilities
- Test WAF protection against sample attacks

---

## 🔄 CloudFront and WAF Integration

### Description
Integrating CloudFront with WAF provides a comprehensive solution for delivering content securely and efficiently while protecting it from various threats.

### Key Concepts
- **Edge Protection**: Filtering malicious traffic at CloudFront edge locations
- **Global Defense**: Uniform security across all geographic regions
- **Bot Control**: Identifying and managing bot traffic
- **Application Layer Protection**: Defending against SQL injection, XSS, and other attacks

### What You Do in the Lab
- Associate WAF web ACL with CloudFront distribution
- Configure CloudFront to forward necessary headers to WAF
- Test end-to-end protection and delivery of content
- Review logs to understand traffic patterns and security events

---

## ✅ Summary

| Resource                | Purpose                                             | Created Using    |
|-------------------------|-----------------------------------------------------|------------------|
| S3 Bucket               | Store static content as CloudFront origin           | Console & CLI    |
| CloudFront Distribution | Deliver content globally with low latency           | Console & CLI    |
| Origin Access Identity  | Secure access to S3 bucket content                  | Console & CLI    |
| WAF Web ACL             | Filter and protect against malicious requests       | Console & CLI    |
| WAF Rules               | Define specific conditions for request filtering    | Console & CLI    |
| CloudFront Logs         | Track usage and analyze requests                    | Console & CLI    |

This lab provides hands-on experience with Amazon CloudFront and AWS WAF, teaching you how to implement a secure, high-performance content delivery solution that protects your applications from common web threats while providing optimal performance for legitimate users worldwide. 