# LAB09: Configure Route 53 with a Custom Domain (Console & CLI)

## 🌍 Lab Overview

In this lab, you'll use **Amazon Route 53** to set up domain name resolution for AWS resources like S3-hosted websites or EC2 instances. You'll learn how to register a domain (or use an existing one), configure **DNS records**, and link your services to human-readable domain names - essential skills for deploying professional, production-ready applications on AWS.

### 🏗️ Real-World DevOps Relevance

DNS management is a critical component of infrastructure management for several reasons:

- **User Experience**: Users access applications via domain names, not IP addresses or endpoints
- **Service Flexibility**: DNS allows you to change backend infrastructure without changing user-facing URLs
- **High Availability**: DNS enables advanced routing strategies for failover, load balancing, and geo-targeting
- **Multi-Environment Management**: Different subdomains can route to different environments (dev, staging, production)

In professional DevOps workflows, Route 53 is often integrated with:
- **CI/CD Pipelines**: Automated DNS updates when new services are deployed
- **Infrastructure as Code (IaC)**: DNS configuration defined in CloudFormation or Terraform
- **Blue-Green Deployments**: Gradual traffic shifting between old and new versions
- **Disaster Recovery**: Cross-region failover automation

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Register a new domain or configure an existing domain with Route 53
- Create and configure a hosted zone for DNS management
- Configure different types of DNS records (A, CNAME, Alias)
- Route traffic to AWS resources like S3 websites and EC2 instances
- Implement DNS management using both the console and AWS CLI
- Understand DNS propagation and troubleshooting techniques

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- A public-facing resource to point your domain to:
  - EC2 instance with a web server (from LAB01)
  - S3 bucket configured for static website hosting (from LAB02)
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- **OPTIONAL**: A domain name you already own (if you don't want to purchase a new one)

---

## 🖥️ Part 1: Register or Import a Domain

Route 53 allows you to register new domains or use domains registered elsewhere. Choose the option that works best for you.

### Option A: Register a New Domain (involves cost)

1. Sign in to the AWS Management Console
2. Navigate to **Route 53** service
3. In the left navigation pane, choose **Registered domains**
4. Choose **Register Domain**
5. Enter a domain name to check its availability (e.g., `yourdomain.com`)
6. Select an available domain from the results
7. Choose **Add to cart** and then **Continue**
8. Enter your contact information (this will be used for WHOIS records)
9. Review your information and complete the purchase
10. Domain registration can take up to 3 days, but often completes within a few hours

### Option B: Use an Existing Domain (from another registrar)

1. Navigate to **Route 53** service
2. Make a note of the Route 53 nameservers that will be assigned to your hosted zone (we'll configure these later)
3. You'll update your domain's nameservers at your current registrar in a later step

### Option C: Use Route 53 with a Subdomain (for testing without domain purchase)

For lab purposes, you can simulate the process with a hypothetical domain:

1. Proceed with the next parts of the lab using a hypothetical domain (e.g., `example-lab.com`)
2. You won't be able to fully test resolution, but you'll learn the configuration process

---

## 📦 Part 2: Create a Hosted Zone

A hosted zone is a container for DNS records for your domain. It defines how Route 53 responds to DNS queries.

### Step 1: Create a Public Hosted Zone

1. In the Route 53 console, navigate to **Hosted zones**
2. Click **Create hosted zone**
3. Configure the zone:
   - **Domain name**: Enter your domain (e.g., `yourdomain.com`)
   - **Type**: Select **Public hosted zone**
   - **Comment** (optional): Add a description like "Main domain for lab resources"
4. Click **Create hosted zone**

### Step 2: Note the Nameservers

1. After your hosted zone is created, you'll see NS (nameserver) records automatically created
2. Make a note of these four nameserver addresses (they'll look like `ns-123.awsdns-45.com`)
3. If using an existing domain (Option B above), you'll need to update your domain's nameservers at your registrar:
   - Log in to your domain registrar (e.g., GoDaddy, Namecheap)
   - Find the nameserver settings for your domain
   - Replace the current nameservers with the four Route 53 nameservers
   - Save the changes (propagation can take 24-48 hours)

---

## 🌐 Part 3: Add DNS Records

DNS records route traffic to specific resources. You'll create records based on the type of resource you want to point to.

### Option A: Point to an S3 Static Website

Prerequisites:
- You've configured an S3 bucket for static website hosting
- You've noted the S3 website endpoint (e.g., `your-bucket-name.s3-website-region.amazonaws.com`)

#### Step 1: Create an Alias Record for Root Domain (apex)

1. In your hosted zone, click **Create record**
2. Keep the **Record name** field empty (for the root domain)
3. Record type: **A - Routes traffic to an IPv4 address and some AWS resources**
4. Toggle on **Alias**
5. In **Route traffic to**:
   - Select **Alias to S3 website endpoint**
   - Select your region
   - Select your S3 bucket from the dropdown
6. **Routing policy**: Simple routing
7. Click **Create records**

#### Step 2: Create a CNAME Record for WWW Subdomain

1. Click **Create record** again
2. **Record name**: `www`
3. **Record type**: **CNAME - Routes traffic to another domain name**
4. **Value**: Enter your S3 website endpoint (e.g., `your-bucket-name.s3-website-region.amazonaws.com`)
5. **TTL**: 300 seconds
6. **Routing policy**: Simple routing
7. Click **Create records**

### Option B: Point to an EC2 Instance

Prerequisites:
- You have an EC2 instance running a web server
- You've noted the public IP address of your instance

#### Step 1: Create an A Record for Root Domain

1. In your hosted zone, click **Create record**
2. Keep the **Record name** field empty (for the root domain)
3. **Record type**: **A - Routes traffic to an IPv4 address and some AWS resources**
4. **Value**: Enter the public IP address of your EC2 instance
5. **TTL**: 300 seconds
6. **Routing policy**: Simple routing
7. Click **Create records**

#### Step 2: Create an A Record for WWW Subdomain

1. Click **Create record** again
2. **Record name**: `www`
3. **Record type**: **A - Routes traffic to an IPv4 address and some AWS resources**
4. **Value**: Enter the public IP address of your EC2 instance
5. **TTL**: 300 seconds
6. **Routing policy**: Simple routing
7. Click **Create records**

> **Note**: For production systems, consider using an Elastic IP address for your EC2 instance to ensure the IP address doesn't change if the instance is stopped and started.

---

## 💻 Part 4: Manage DNS Records with AWS CLI

The AWS CLI provides a powerful way to automate DNS management for infrastructure as code and CI/CD pipelines.

### Step 1: Prepare a JSON Change Batch File

Create a file named `dns-changes.json` with the following content (modify for your domain and values):

```json
{
  "Changes": [
    {
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "api.yourdomain.com",
        "Type": "A",
        "TTL": 300,
        "ResourceRecords": [
          {
            "Value": "203.0.113.25"
          }
        ]
      }
    }
  ]
}
```

### Step 2: Apply the Changes Using the CLI

```bash
# Get your hosted zone ID first (replace DOMAIN with your domain)
ZONEID=$(aws route53 list-hosted-zones-by-name --dns-name "yourdomain.com" --query "HostedZones[0].Id" --output text | sed 's/\/hostedzone\///')

# Apply the changes
aws route53 change-resource-record-sets \
  --hosted-zone-id $ZONEID \
  --change-batch file://dns-changes.json
```

### Step 3: Create a CNAME Record via CLI

```bash
# Create a JSON change batch for a CNAME
cat > cname-record.json << EOF
{
  "Changes": [
    {
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "blog.yourdomain.com",
        "Type": "CNAME",
        "TTL": 300,
        "ResourceRecords": [
          {
            "Value": "my-blog.s3-website-us-east-1.amazonaws.com"
          }
        ]
      }
    }
  ]
}
EOF

# Apply the changes
aws route53 change-resource-record-sets \
  --hosted-zone-id $ZONEID \
  --change-batch file://cname-record.json
```

### Step 4: List Records Using CLI

```bash
# List all records in your hosted zone
aws route53 list-resource-record-sets --hosted-zone-id $ZONEID
```

---

## 🔍 Part 5: Testing and Troubleshooting DNS Configuration

### Step 1: Verify DNS Resolution

1. Wait for DNS propagation (usually 5-10 minutes with the TTL we set, but can be longer)
2. Open a browser and navigate to your domain (e.g., `http://yourdomain.com`)
3. Also test the www subdomain (e.g., `http://www.yourdomain.com`)

### Step 2: Troubleshoot Common Issues

If your domain doesn't resolve correctly:

#### Check DNS Propagation

```bash
# Check domain resolution
dig yourdomain.com

# Check nameserver configuration
dig NS yourdomain.com

# Check specific record
dig A www.yourdomain.com
```

#### Verify Resource Availability

- For S3: Check that the bucket is properly configured for static website hosting
- For EC2: Ensure security groups allow HTTP traffic (port 80) and the web server is running

#### Verify Route 53 Configuration

1. Confirm nameservers are correctly set at your registrar
2. Verify A and CNAME records point to correct resources
3. Check for typos in domain names or IP addresses

---

## 🧪 Challenge: Advanced DNS Configurations

Complete these additional tasks to expand your Route 53 knowledge:

### Challenge 1: Set Up Email with MX Records

Create MX records to enable email for your domain (if you have an email provider):

1. In your hosted zone, click **Create record**
2. **Record name**: Leave empty (for root domain)
3. **Record type**: **MX - Routes email to mail servers**
4. **Value**: Enter priority and mail server (e.g., `10 inbound-smtp.us-east-1.amazonaws.com`)
5. **TTL**: 3600 seconds
6. Click **Create records**

### Challenge 2: Implement Weighted Routing for Testing

Create weighted records to distribute traffic between two different servers:

1. Create two EC2 instances with different content
2. In your hosted zone, create two weighted A records for the same subdomain:
   - Record 1: Weight 80%, pointing to the first EC2 IP
   - Record 2: Weight 20%, pointing to the second EC2 IP
3. Test by refreshing your browser multiple times

### Challenge 3: Create a Health Check and Failover Configuration

1. Create a Route 53 health check for your primary EC2 instance
2. Create a failover record group with primary and secondary resources
3. Test by stopping your primary instance temporarily

---

## 🧹 Cleanup

To avoid incurring charges after completing this lab, clean up all resources:

### Console Cleanup:

1. If you created a domain specifically for this lab and don't want to keep it:
   - Navigate to **Route 53 > Registered domains**
   - Select your domain and follow the process to disable automatic renewal
   - (Note: You cannot immediately delete a registered domain, but you can let it expire)

2. Delete the hosted zone:
   - Navigate to **Route 53 > Hosted zones**
   - Select your hosted zone
   - Click **Delete hosted zone**
   - Confirm deletion

3. Don't forget to clean up any resources you pointed to:
   - Terminate EC2 instances
   - Delete S3 buckets

### CLI Cleanup:

```bash
# Delete the hosted zone (get the zone ID first)
ZONEID=$(aws route53 list-hosted-zones-by-name --dns-name "yourdomain.com" --query "HostedZones[0].Id" --output text | sed 's/\/hostedzone\///')

# Delete all records except NS and SOA
aws route53 list-resource-record-sets --hosted-zone-id $ZONEID --query "ResourceRecordSets[?!(Type=='NS' || Type=='SOA')]" > records-to-delete.json

# Modify the output file to create a proper change batch for deletion
# Note: This requires jq to be installed
jq '{ "Changes": [ .[] | {"Action": "DELETE", "ResourceRecordSet": .} ] }' records-to-delete.json > delete-batch.json

# Delete the records
aws route53 change-resource-record-sets \
  --hosted-zone-id $ZONEID \
  --change-batch file://delete-batch.json

# Delete the hosted zone
aws route53 delete-hosted-zone --id $ZONEID

# Clean up local files
rm -f dns-changes.json cname-record.json records-to-delete.json delete-batch.json
```

---

## ✅ Key Takeaways

In this lab, you've learned:

- How to register and manage domains with Route 53
- How to create and configure hosted zones for DNS management
- How to create different types of DNS records for various use cases
- How to use alias records to point to AWS resources
- How to manage DNS configurations via the AWS CLI
- How to troubleshoot DNS issues
- How DNS integrates with other AWS services

These skills are essential for managing professional cloud infrastructure, enabling you to create user-friendly URLs for your applications and implement advanced traffic management strategies.

## 📚 Additional Resources

- [Route 53 Developer Guide](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)
- [DNS Record Types Explained](https://aws.amazon.com/premiumsupport/knowledge-center/route-53-dns-record-types/)
- [Route 53 Routing Policies](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html)
- [Best Practices for DNS Management](https://aws.amazon.com/blogs/networking-and-content-delivery/best-practices-for-dns-management-with-amazon-route-53/)
- [CloudFormation Examples for Route 53](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-route53-recordset.html)

---

Happy Domain Management! 🌐