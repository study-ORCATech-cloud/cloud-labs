# AWS/LAB02-S3

## 📄 Lab 02: Create and Manage an S3 Bucket

Welcome to Lab 02! In this hands-on lab, you'll learn how to create and manage an Amazon S3 (Simple Storage Service) bucket using both the **AWS Management Console** and the **AWS CLI**. This lab introduces you to object storage in AWS and helps you understand use cases for static content hosting, data archiving, and backups.

---

## 🔎 What You'll Learn

- What Amazon S3 is and how it works
- How to create an S3 bucket using the AWS Console and CLI
- How to upload, list, and delete files in a bucket
- How to set bucket permissions and enable static website hosting

---

## 🎓 Prerequisites

- An AWS account
- AWS CLI installed and configured (`aws configure`)
- Basic understanding of cloud storage concepts

---

## 🏠 Part 1: Create an S3 Bucket (Console)

1. Go to the [AWS S3 Console](https://s3.console.aws.amazon.com/s3/home).
2. Click **"Create bucket"**.
3. Fill in the required details:
   - **Bucket name**: Must be globally unique (e.g., `my-lab02-bucket-12345`)
   - **Region**: Choose your preferred region
   - Leave the rest as default for now
4. Click **"Create bucket"**.

---

## 📁 Part 2: Upload and Manage Files (Console)

1. Click your newly created bucket.
2. Click **"Upload"** and add any text or image file.
3. After upload, explore the object options (download, make public, delete).
4. Try enabling **Static Website Hosting** (Properties tab):
   - Enable hosting
   - Set index document as `index.html`
   - Upload a basic HTML file

---

## ⌚ Part 3: Create and Use Bucket via AWS CLI

### Create Bucket:
```bash
aws s3 mb s3://my-lab02-cli-bucket --region us-east-1
```

### Upload File:
```bash
echo "Hello from CLI" > hello.txt
aws s3 cp hello.txt s3://my-lab02-cli-bucket/
```

### List Objects:
```bash
aws s3 ls s3://my-lab02-cli-bucket
```

### Delete File:
```bash
aws s3 rm s3://my-lab02-cli-bucket/hello.txt
```

### Delete Bucket:
```bash
aws s3 rb s3://my-lab02-cli-bucket --force
```

---

## 🤔 Challenge Task

1. Create an S3 bucket named after your initials and date (e.g., `js-lab02-20250423`).
2. Upload a custom `index.html` page.
3. Enable static website hosting.
4. Access your website via the endpoint provided.

---

## 📅 Cleanup

To avoid unnecessary charges:
- Delete uploaded files from S3.
- Delete the bucket from the Console or using:
```bash
aws s3 rb s3://your-bucket-name --force
```

---

## 🚀 You're All Set!

You now know how to:
- Use S3 buckets for cloud storage
- Perform basic operations via Console and CLI
- Host static sites in S3

This foundational knowledge will be useful in automation, web hosting, and data pipelines. Onward to Lab 03!

