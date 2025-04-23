# AWS LAB02: Resource Overview

## 🔧 What Resources Are We Working With?

In **AWS LAB02**, we’re diving into Amazon S3 (Simple Storage Service), a foundational storage service in AWS. S3 provides highly scalable, durable, and secure object storage. In this lab, you will interact with S3 buckets to understand how data is stored, accessed, and managed in the cloud.

---

## 📂 S3 Bucket

### Description
An S3 bucket is a container for storing objects (files). Each bucket has a globally unique name and is tied to a specific AWS region.

### Key Concepts
- **Bucket Name**: Must be globally unique
- **Region**: The geographical location where your data is stored
- **Storage Class**: Determines the cost and redundancy (not covered in this basic lab)
- **Objects**: Individual files stored in a bucket
- **Folder**: Logical grouping of objects (S3 is flat, but folders are a UI abstraction)

### What You Do in the Lab
- Create an S3 bucket using both the AWS Console and CLI
- Upload and manage files (objects)
- Delete objects and buckets
- Use naming conventions to keep your work organized

---

## 👥 Bucket Policies (Optional)

### Description
Bucket policies define access permissions at the bucket level using JSON-based policy documents.

### Key Concepts
- **Public Access Settings**: S3 buckets are private by default
- **Permissions**: Who can read/write/delete objects
- **Use Cases**: Static website hosting, read-only file sharing

### What You Might Do (Optional/Challenge)
- Enable public read access to specific files (or the entire bucket)
- Host a static website by enabling website hosting on the bucket and uploading HTML

---

## ⌚ CLI Commands Used in This Lab

| Command                       | Description                             |
|------------------------------|-----------------------------------------|
| `aws s3 mb`                  | Make (create) a new bucket              |
| `aws s3 cp`                  | Copy/upload files to/from S3            |
| `aws s3 ls`                  | List contents of a bucket               |
| `aws s3 rm`                  | Delete objects from a bucket            |
| `aws s3 rb --force`          | Remove a bucket and its contents        |

---

## ✅ Summary

| Resource       | Purpose                                  | Created Using |
|----------------|-------------------------------------------|----------------|
| S3 Bucket      | Stores files and data in the cloud        | Console & CLI  |
| Object         | A file stored in an S3 bucket              | Uploaded       |
| Bucket Policy  | (Optional) Grants access to bucket/files  | Edited (UI/JSON) |

Amazon S3 is an essential part of many cloud-based applications, from hosting static websites to storing backups and large datasets. Mastering this service will give you a strong foundation in cloud storage concepts.

You're now ready to build, store, and manage content in the cloud!

