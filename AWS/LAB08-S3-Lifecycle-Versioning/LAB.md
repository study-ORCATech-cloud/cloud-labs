# LAB08: Implement S3 Lifecycle Rules and Versioning (Console & CLI)

## 🔁 Lab Overview

In this lab, you'll automate data retention and version control using **Amazon S3**. You'll enable **versioning**, create **lifecycle policies**, and explore how to manage object aging, transitions, and deletions. This helps reduce storage costs and ensure recoverability.

---

## 🎯 Objectives

- Enable versioning on an S3 bucket
- Upload and modify objects to generate versions
- Create a lifecycle rule to move data to infrequent access
- Expire old object versions automatically

---

## 🛠️ Prerequisites

- AWS account
- AWS CLI installed and configured

---

## 📂 Part 1: Create Versioned S3 Bucket

1. Go to **S3 > Create bucket**
2. Bucket name: `lab08-versioned-bucket-<yourname>`
3. Region: `us-east-1`
4. Enable **bucket versioning** during setup

### Upload & Modify Objects
1. Upload a file `notes.txt`
2. Upload a new version of `notes.txt`
3. Go to **Versions** tab to see history

---

## 🔁 Part 2: Add Lifecycle Rule (Console)

1. Navigate to **Management > Lifecycle rules**
2. Rule name: `transition-old-versions`
3. Apply to **previous versions** of objects
4. Actions:
   - Transition to **Glacier Instant Retrieval** after 1 day
   - Expire previous versions after 7 days

---

## 💻 Part 3: Enable and Manage via CLI
```bash
# Enable versioning on existing bucket
aws s3api put-bucket-versioning \
  --bucket lab08-versioned-bucket-<yourname> \
  --versioning-configuration Status=Enabled

# Create lifecycle config JSON
cat > lifecycle.json << EOF
{
  "Rules": [
    {
      "ID": "ExpireOldVersions",
      "Status": "Enabled",
      "NoncurrentVersionExpiration": {
        "NoncurrentDays": 7
      },
      "NoncurrentVersionTransitions": [
        {
          "NoncurrentDays": 1,
          "StorageClass": "GLACIER_IR"
        }
      ]
    }
  ]
}
EOF

# Apply rule
aws s3api put-bucket-lifecycle-configuration \
  --bucket lab08-versioned-bucket-<yourname> \
  --lifecycle-configuration file://lifecycle.json
```

---

## 🧪 Challenge

- Add tag-based lifecycle rule (e.g., only archive objects with Tag: `archive=true`)
- Restore and download an older object version

---

## 🧹 Cleanup

- Remove lifecycle rules
- Delete all versions of objects
- Delete the bucket

---

## ✅ Summary

You’ve now learned how to manage object versions and automate transitions and deletions. This lab builds essential knowledge for cost-efficient, policy-driven storage in cloud-native environments.

