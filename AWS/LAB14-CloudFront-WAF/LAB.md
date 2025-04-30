# LAB14: Securing and Optimizing Content Delivery with CloudFront and WAF

## ⚡ Lab Overview

In this advanced lab, you'll work with **Amazon CloudFront** and **AWS WAF (Web Application Firewall)**, two powerful services that together provide a secure, high-performance content delivery solution. You'll learn how to set up a global content delivery network to serve content with low latency to users worldwide, while implementing robust security controls to protect your web applications from common threats and attacks.

### 💼 Real-World DevOps Applications

CloudFront and WAF are essential components in modern web application architecture, offering several key advantages:

- **Global Scale**: Deliver content with low latency to users around the world
- **Security**: Protect web applications from common vulnerabilities and attacks
- **Performance**: Reduce origin load and improve user experience with edge caching
- **Cost Efficiency**: Reduce data transfer costs and origin server load
- **Reliability**: Ensure high availability even during traffic spikes

Common production use cases include:

- **Static Website Hosting**: Delivering HTML, CSS, JavaScript, and images
- **Dynamic API Acceleration**: Caching API responses at edge locations
- **Media Distribution**: Efficiently delivering video and audio content
- **Security Layer**: Protecting web applications from DDoS attacks, SQL injection, and XSS
- **Mobile App Backends**: Accelerating API calls from mobile applications
- **Software Distribution**: Distributing software updates and downloads

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure an Amazon CloudFront distribution
- Set up an S3 bucket as an origin for CloudFront
- Implement Origin Access Identity (OAI) for secure S3 access
- Create and configure an AWS WAF web ACL with various rule types
- Associate a WAF web ACL with a CloudFront distribution
- Configure rate-based rules to prevent brute force attacks
- Implement AWS managed rule sets for common vulnerabilities
- Test CloudFront and WAF configurations
- Monitor CloudFront and WAF with AWS CloudWatch

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of web hosting and content delivery concepts
- Familiarity with web security principles
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and configure CloudFront and WAF resources using the AWS Management Console.

## 📊 Step 1: Create an S3 Bucket for Content Origin

First, let's create an S3 bucket to store the static content that will be delivered through CloudFront.

1. Sign in to the AWS Management Console
2. Navigate to the **Amazon S3** service
3. Click **Create bucket**
4. For **Bucket name**, enter `lab14-cloudfront-origin-[RANDOM_STRING]` (replace [RANDOM_STRING] with a unique identifier, like your initials and a random number)
5. For **AWS Region**, select **EU (Ireland) eu-west-1**
6. Under **Object Ownership**, select **ACLs disabled (recommended)**
7. Under **Block Public Access settings for this bucket**, keep all settings enabled (default)
8. Under **Bucket Versioning**, select **Disable**
9. Leave other settings as default
10. Click **Create bucket**

## 📁 Step 2: Add Sample Content to the S3 Bucket

Let's upload some sample content to serve through CloudFront.

1. Select the bucket you just created
2. Click **Upload**
3. Click **Add files** and create or select an HTML file from your computer
   - If you don't have an HTML file, you can create a simple one:
     - Create a file named `index.html` with the following content:
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <title>CloudFront Lab Test Page</title>
         <style>
             body {
                 font-family: Arial, sans-serif;
                 margin: 40px;
                 line-height: 1.6;
             }
             h1 {
                 color: #0066cc;
             }
         </style>
     </head>
     <body>
         <h1>Hello from CloudFront!</h1>
         <p>This page is being served through Amazon CloudFront from an S3 origin.</p>
         <p>Current time: <span id="current-time"></span></p>
         
         <script>
             document.getElementById('current-time').textContent = new Date().toLocaleString();
         </script>
     </body>
     </html>
     ```
4. Also create or upload an image file (e.g., any simple JPG, PNG, or GIF)
5. Click **Upload**

## 🌐 Step 3: Create a CloudFront Distribution

Now, let's create a CloudFront distribution to serve the content from our S3 bucket.

1. Navigate to the **Amazon CloudFront** service
2. Click **Create distribution**
3. For **Origin domain**, select your S3 bucket from the dropdown (`lab14-cloudfront-origin-[RANDOM_STRING]`)
4. For **Origin access**, select **Origin access control settings (recommended)**
5. Click **Create control setting**
   - For **Name**, enter `lab14-oac`
   - Leave other settings as default
   - Click **Create**
6. For **Default cache behavior**, configure:
   - **Viewer protocol policy**: Select **Redirect HTTP to HTTPS**
   - **Allowed HTTP methods**: Select **GET, HEAD**
   - **Cache key and origin requests**: Leave as **Cache policy and origin request policy (recommended)**
   - **Cache policy**: Select **CachingOptimized**
   - **Origin request policy**: Leave as **None**
   - **Response headers policy**: Leave as **None**
7. For **Settings**, configure:
   - **Price class**: Select **Use only North America and Europe** (to reduce costs for this lab)
   - **WAF**: We'll add this later, leave as **Do not enable security protections**
   - **Default root object**: Enter `index.html`
8. Leave all other settings as default
9. Click **Create distribution**

## ☁️ Step 4: Update S3 Bucket Policy for CloudFront Access

After creating the distribution, you'll see a notification about updating your bucket policy. Let's do that:

1. Click **Copy policy**
2. Navigate back to the **Amazon S3** service
3. Select your bucket
4. Click on the **Permissions** tab
5. In the **Bucket policy** section, click **Edit**
6. Paste the copied policy into the editor
7. Click **Save changes**

## 🔄 Step 5: Wait for CloudFront Distribution Deployment

1. Navigate back to the **CloudFront** service
2. Your distribution will be in a **Deploying** state
3. Wait until the **Status** changes to **Deployed** (this may take 5-10 minutes)
4. Once deployed, note the **Distribution domain name** (it will look like `dxxxxxxxx.cloudfront.net`)

## 🔍 Step 6: Test the CloudFront Distribution

1. Open a new browser tab
2. Enter the CloudFront domain name you noted earlier, followed by `/index.html` (e.g., `https://dxxxxxxxx.cloudfront.net/index.html`)
3. You should see your HTML page being served through CloudFront
4. Test the image file by appending its filename to the CloudFront domain name

## 🛡️ Step 7: Create a WAF Web ACL

Now, let's create a Web ACL in AWS WAF to protect our CloudFront distribution.

1. Navigate to the **AWS WAF & Shield** service
2. In the left navigation pane, click **Web ACLs**
3. Click **Create web ACL**
4. For **Name**, enter `lab14-cloudfront-waf`
5. For **Description**, enter `Web ACL for CloudFront Lab`
6. For **Resource type**, select **CloudFront distributions**
7. For **Region**, it should automatically select **Global (CloudFront)**
8. Click **Next**

## 🔒 Step 8: Add Managed Rule Groups to the Web ACL

1. In the **Add rules and rule groups** section, click **Add rules** > **Add managed rule groups**
2. Under **AWS managed rule groups**, expand **Core rule set** and click **Add to web ACL**
3. Also add the **Amazon IP reputation list** rule group by expanding it and clicking **Add to web ACL**
4. Click **Next**

## ⚡ Step 9: Add a Rate-Based Rule

1. Click **Add rules** > **Add rate-based rule**
2. For **Name**, enter `lab14-rate-limit`
3. For **Rate limit**, enter `100` (This means any IP that exceeds 100 requests in 5 minutes will be blocked)
4. Leave **Scope-down statement** as **None**
5. Click **Add rule**
6. Click **Next**

## 👮 Step 10: Set Default Action and Create the Web ACL

1. For **Default action**, select **Allow**
2. Review your configuration and click **Next**
3. In the **Set rule priority** page, leave the default order and click **Next**
4. In the **Configure metrics** page, leave the default settings and click **Next**
5. In the **Review and create** page, review all settings and click **Create web ACL**

## 🔗 Step 11: Associate Web ACL with CloudFront Distribution

1. On the web ACL details page, click the **Associated AWS resources** tab
2. Click **Add association**
3. For **Resource type**, select **CloudFront distribution**
4. For **Amazon CloudFront distribution**, select your CloudFront distribution (the one you created earlier)
5. Click **Add**

## 🔄 Step 12: Wait for CloudFront Distribution Update

1. Navigate back to the **CloudFront** service
2. Your distribution will be in an **Updating** state
3. Wait until the **Status** changes to **Deployed** (this may take a few minutes)

## 🧪 Step 13: Test WAF Protection

Let's test the WAF protection with a simulated attack:

1. Open a new browser tab
2. Enter the CloudFront domain name followed by a common SQL injection attack pattern:
   ```
   https://dxxxxxxxx.cloudfront.net/index.html?id=1' OR '1'='1
   ```
3. You should see a block page or an access denied error (403 Forbidden), indicating that the WAF has blocked the request

## 📊 Step 14: Check WAF Metrics and Logs

1. Navigate to the **AWS WAF & Shield** service
2. Click on **Web ACLs** and select your web ACL (`lab14-cloudfront-waf`)
3. Click on the **Traffic overview** tab to see metrics about the requests that have been allowed or blocked
4. Review the **Sampled requests** tab to see examples of requests that matched rules

## 🧹 Step 15: Console Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the console part of this lab:

1. Navigate to the **AWS WAF & Shield** service
2. Click on **Web ACLs** and select your web ACL (`lab14-cloudfront-waf`)
3. Click the **Associated AWS resources** tab
4. Select the association with your CloudFront distribution and click **Disassociate**
5. After disassociation completes, go to the web ACL detail page
6. Click **Delete**
7. Confirm by typing the name of the web ACL and click **Delete**

8. Navigate to the **CloudFront** service
9. Select your distribution
10. Click **Disable**
11. Wait for the distribution to reach a **Disabled** state (this may take a few minutes)
12. Once disabled, select the distribution again and click **Delete**
13. Confirm the deletion

14. Navigate to the **S3** service
15. Select your bucket
16. Click **Empty**
17. Type "permanently delete" in the confirmation field and click **Empty**
18. After the bucket is empty, click **Delete**
19. Type the bucket name in the confirmation field and click **Delete bucket**

---

# 💻 PART 2: AWS CLI IMPLEMENTATION

In this second part of the lab, you'll create and configure CloudFront and WAF resources using the AWS Command Line Interface (CLI).

## 📊 Step 1: Create an S3 Bucket for Content Origin (CLI)

```bash
# Generate a random string to ensure bucket name uniqueness
RANDOM_STRING=$(date +%s | cut -c 5-10)
BUCKET_NAME="lab14-cloudfront-origin-cli-$RANDOM_STRING"

# Create the S3 bucket
aws s3 mb s3://$BUCKET_NAME --region eu-west-1

echo "Created bucket: $BUCKET_NAME"
echo "BUCKET_NAME=$BUCKET_NAME" > lab14-variables.sh
```

## 📁 Step 2: Add Sample Content to the S3 Bucket (CLI)

```bash
# Create a temporary directory for our files
mkdir -p temp_content

# Create a simple HTML file
cat > temp_content/index.html << EOL
<!DOCTYPE html>
<html>
<head>
    <title>CloudFront CLI Lab Test Page</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 40px;
            line-height: 1.6;
        }
        h1 {
            color: #0066cc;
        }
    </style>
</head>
<body>
    <h1>Hello from CloudFront! (CLI Version)</h1>
    <p>This page is being served through Amazon CloudFront from an S3 origin.</p>
    <p>This distribution was created using the AWS CLI.</p>
    <p>Current time: <span id="current-time"></span></p>
    
    <script>
        document.getElementById('current-time').textContent = new Date().toLocaleString();
    </script>
</body>
</html>
EOL

# Create a simple image
# This uses base64 to create a small colored square GIF
cat > temp_content/square.gif.base64 << EOL
R0lGODlhPAA8AIABAAAAAP///yH5BAEKAAEALAAAAAA8ADwAAAJVjI+py+0Po5y02ouz3rz7D4biSJbmiabqyrbuC8fyTNf2jef6zvf+DwwKh8Si8YhMKpfMpvMJjUqn1Kr1is1qt9yu9wsOi8fksvmMTqvX7Lb7DY8KAQA7
EOL
base64 -d temp_content/square.gif.base64 > temp_content/square.gif
rm temp_content/square.gif.base64

# Source our variables file to get the bucket name
source lab14-variables.sh

# Upload files to S3
aws s3 cp temp_content/index.html s3://$BUCKET_NAME/
aws s3 cp temp_content/square.gif s3://$BUCKET_NAME/

echo "Files uploaded to S3 bucket"
```

## 🔑 Step 3: Create an Origin Access Control for CloudFront (CLI)

```bash
# Create an Origin Access Control configuration
cat > oac-config.json << EOL
{
  "Name": "lab14-oac-cli",
  "Description": "OAC for lab14 CloudFront distribution",
  "SigningProtocol": "sigv4",
  "SigningBehavior": "always",
  "OriginAccessControlOriginType": "s3"
}
EOL

# Create the OAC and save the ID
OAC_ID=$(aws cloudfront create-origin-access-control --origin-access-control-config file://oac-config.json --query 'OriginAccessControl.Id' --output text)

echo "Created Origin Access Control with ID: $OAC_ID"
echo "OAC_ID=$OAC_ID" >> lab14-variables.sh
```

## 🌐 Step 4: Create a CloudFront Distribution (CLI)

```bash
# Source our variables file to get the bucket name and OAC ID
source lab14-variables.sh

# Create a CloudFront distribution configuration
cat > cf-config.json << EOL
{
  "CallerReference": "cli-lab14-$(date +%s)",
  "Aliases": {
    "Quantity": 0
  },
  "DefaultRootObject": "index.html",
  "Origins": {
    "Quantity": 1,
    "Items": [
      {
        "Id": "S3Origin",
        "DomainName": "${BUCKET_NAME}.s3.eu-west-1.amazonaws.com",
        "OriginPath": "",
        "S3OriginConfig": {
          "OriginAccessIdentity": ""
        },
        "OriginAccessControlId": "${OAC_ID}"
      }
    ]
  },
  "DefaultCacheBehavior": {
    "TargetOriginId": "S3Origin",
    "ViewerProtocolPolicy": "redirect-to-https",
    "AllowedMethods": {
      "Quantity": 2,
      "Items": [
        "GET",
        "HEAD"
      ],
      "CachedMethods": {
        "Quantity": 2,
        "Items": [
          "GET",
          "HEAD"
        ]
      }
    },
    "CachePolicyId": "658327ea-f89d-4fab-a63d-7e88639e58f6",
    "Compress": true,
    "FieldLevelEncryptionId": ""
  },
  "PriceClass": "PriceClass_100",
  "Enabled": true,
  "Comment": "Lab14 CloudFront Distribution created via CLI"
}
EOL

# Create the CloudFront distribution
DISTRIBUTION_INFO=$(aws cloudfront create-distribution --distribution-config file://cf-config.json)
DISTRIBUTION_ID=$(echo $DISTRIBUTION_INFO | jq -r '.Distribution.Id')
DISTRIBUTION_DOMAIN=$(echo $DISTRIBUTION_INFO | jq -r '.Distribution.DomainName')

echo "Created CloudFront distribution with ID: $DISTRIBUTION_ID"
echo "Distribution domain: $DISTRIBUTION_DOMAIN"
echo "DISTRIBUTION_ID=$DISTRIBUTION_ID" >> lab14-variables.sh
echo "DISTRIBUTION_DOMAIN=$DISTRIBUTION_DOMAIN" >> lab14-variables.sh
```

## ☁️ Step 5: Update S3 Bucket Policy for CloudFront Access (CLI)

```bash
# Source our variables file
source lab14-variables.sh

# Create a bucket policy for CloudFront access
cat > bucket-policy.json << EOL
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipal",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::${BUCKET_NAME}/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::$(aws sts get-caller-identity --query Account --output text):distribution/${DISTRIBUTION_ID}"
        }
      }
    }
  ]
}
EOL

# Apply the bucket policy
aws s3api put-bucket-policy --bucket $BUCKET_NAME --policy file://bucket-policy.json

echo "Updated S3 bucket policy for CloudFront access"
```

## 🔄 Step 6: Wait for CloudFront Distribution Deployment (CLI)

```bash
# Source our variables file
source lab14-variables.sh

echo "Waiting for CloudFront distribution to deploy (this may take 5-10 minutes)..."
aws cloudfront wait distribution-deployed --id $DISTRIBUTION_ID

echo "CloudFront distribution is now deployed!"
echo "You can access your content at: https://$DISTRIBUTION_DOMAIN/index.html"
```

## 🛡️ Step 7: Create a WAF Web ACL (CLI)

```bash
# Create a WAF web ACL configuration
cat > waf-config.json << EOL
{
  "Name": "lab14-cloudfront-waf-cli",
  "Scope": "CLOUDFRONT",
  "DefaultAction": {
    "Allow": {}
  },
  "Description": "Web ACL for CloudFront Lab created via CLI",
  "Rules": [
    {
      "Name": "AWS-AWSManagedRulesCommonRuleSet",
      "Priority": 0,
      "Statement": {
        "ManagedRuleGroupStatement": {
          "VendorName": "AWS",
          "Name": "AWSManagedRulesCommonRuleSet"
        }
      },
      "OverrideAction": {
        "None": {}
      },
      "VisibilityConfig": {
        "SampledRequestsEnabled": true,
        "CloudWatchMetricsEnabled": true,
        "MetricName": "AWS-AWSManagedRulesCommonRuleSet"
      }
    },
    {
      "Name": "AWS-AWSManagedRulesAmazonIpReputationList",
      "Priority": 1,
      "Statement": {
        "ManagedRuleGroupStatement": {
          "VendorName": "AWS",
          "Name": "AWSManagedRulesAmazonIpReputationList"
        }
      },
      "OverrideAction": {
        "None": {}
      },
      "VisibilityConfig": {
        "SampledRequestsEnabled": true,
        "CloudWatchMetricsEnabled": true,
        "MetricName": "AWS-AWSManagedRulesAmazonIpReputationList"
      }
    },
    {
      "Name": "lab14-rate-limit-cli",
      "Priority": 2,
      "Statement": {
        "RateBasedStatement": {
          "Limit": 100,
          "AggregateKeyType": "IP"
        }
      },
      "Action": {
        "Block": {}
      },
      "VisibilityConfig": {
        "SampledRequestsEnabled": true,
        "CloudWatchMetricsEnabled": true,
        "MetricName": "lab14-rate-limit-cli"
      }
    }
  ],
  "VisibilityConfig": {
    "SampledRequestsEnabled": true,
    "CloudWatchMetricsEnabled": true,
    "MetricName": "lab14-cloudfront-waf-cli"
  }
}
EOL

# Create the WAF web ACL
WEB_ACL_ID=$(aws wafv2 create-web-acl --cli-input-json file://waf-config.json --region us-east-1 --query 'Summary.ARN' --output text)

echo "Created WAF web ACL with ARN: $WEB_ACL_ID"
echo "WEB_ACL_ID=$WEB_ACL_ID" >> lab14-variables.sh
```

## 🔗 Step 8: Associate Web ACL with CloudFront Distribution (CLI)

```bash
# Source our variables file
source lab14-variables.sh

# Associate the web ACL with the CloudFront distribution
aws wafv2 associate-web-acl \
  --web-acl-arn $WEB_ACL_ID \
  --resource-arn arn:aws:cloudfront::$(aws sts get-caller-identity --query Account --output text):distribution/$DISTRIBUTION_ID \
  --region us-east-1

echo "Associated WAF web ACL with CloudFront distribution"
```

## 🔄 Step 9: Wait for CloudFront Distribution Update (CLI)

```bash
# Source our variables file
source lab14-variables.sh

echo "Waiting for CloudFront distribution to update (this may take a few minutes)..."
aws cloudfront wait distribution-deployed --id $DISTRIBUTION_ID

echo "CloudFront distribution is now updated with WAF protection!"
echo "You can access your protected content at: https://$DISTRIBUTION_DOMAIN/index.html"
```

## 🧪 Step 10: Test WAF Protection (CLI)

You can test the WAF protection by manually accessing your CloudFront distribution with a SQL injection pattern:

```
https://$DISTRIBUTION_DOMAIN/index.html?id=1' OR '1'='1
```

This should be blocked by the AWS managed rules.

## 🧹 Step 11: CLI Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the CLI part of this lab:

```bash
# Source our variables file
source lab14-variables.sh

# Disassociate WAF web ACL from CloudFront distribution
echo "Disassociating WAF web ACL from CloudFront distribution..."
aws wafv2 disassociate-web-acl \
  --resource-arn arn:aws:cloudfront::$(aws sts get-caller-identity --query Account --output text):distribution/$DISTRIBUTION_ID \
  --region us-east-1

# Delete the WAF web ACL
echo "Deleting WAF web ACL..."
WEB_ACL_ID_SHORT=$(echo $WEB_ACL_ID | cut -d'/' -f3)
aws wafv2 delete-web-acl \
  --name lab14-cloudfront-waf-cli \
  --scope CLOUDFRONT \
  --id $WEB_ACL_ID_SHORT \
  --region us-east-1

# Disable CloudFront distribution
echo "Disabling CloudFront distribution..."
ETAG=$(aws cloudfront get-distribution-config --id $DISTRIBUTION_ID --query 'ETag' --output text)
aws cloudfront get-distribution-config --id $DISTRIBUTION_ID > distribution-config.json
cat distribution-config.json | jq '.DistributionConfig.Enabled = false' > updated-distribution-config.json
aws cloudfront update-distribution --id $DISTRIBUTION_ID --if-match $ETAG --distribution-config file://updated-distribution-config.json

# Wait for distribution to be disabled
echo "Waiting for CloudFront distribution to be disabled (this may take several minutes)..."
aws cloudfront wait distribution-deployed --id $DISTRIBUTION_ID

# Delete CloudFront distribution
echo "Deleting CloudFront distribution..."
ETAG=$(aws cloudfront get-distribution --id $DISTRIBUTION_ID --query 'ETag' --output text)
aws cloudfront delete-distribution --id $DISTRIBUTION_ID --if-match $ETAG

# Delete S3 bucket contents and bucket
echo "Deleting S3 bucket contents..."
aws s3 rm s3://$BUCKET_NAME --recursive

echo "Deleting S3 bucket..."
aws s3 rb s3://$BUCKET_NAME

# Clean up local files
echo "Deleting temporary files..."
rm -rf temp_content
rm -f oac-config.json cf-config.json bucket-policy.json waf-config.json
rm -f distribution-config.json updated-distribution-config.json
rm -f lab14-variables.sh

echo "Cleanup complete!"
```

---

## 🧪 Optional Challenges

To deepen your understanding of CloudFront and WAF, try these additional challenges:

1. **Custom Error Pages**: Configure CloudFront to return custom error pages
2. **CloudFront Functions**: Create a simple CloudFront Function to modify request headers
3. **Geo Restrictions**: Implement geographic restrictions for your CloudFront distribution
4. **Custom WAF Rules**: Create custom WAF rules for specific application requirements
5. **Origin Groups**: Set up CloudFront origin failover with origin groups

---

## 🎓 Summary

In this lab, you've learned how to:
- Create and configure CloudFront distributions with S3 origins
- Implement Origin Access Control for secure S3 access
- Create and configure WAF web ACLs with managed rule sets
- Implement rate-based rules for request limiting
- Associate WAF protections with CloudFront distributions
- Test and monitor CloudFront and WAF configurations

These skills are essential for implementing secure, high-performance content delivery solutions in AWS. CloudFront and WAF work together to provide a robust platform for delivering web content and applications securely to users worldwide, protecting against common threats while ensuring optimal performance and availability. 