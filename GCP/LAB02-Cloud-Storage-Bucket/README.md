# GCP LAB02: Resource Overview

## 🔧 What Resources Are We Working With?

In **GCP LAB02**, we focus on **Google Cloud Storage (GCS)**—a scalable and secure object storage service on GCP. You’ll learn how to create buckets, upload/download objects, manage permissions, and optionally enable static website hosting.

---

## 📂 Cloud Storage Bucket

### Description
Buckets in GCP are top-level containers for storing data (objects). They are globally unique and tied to a project and region.

### Key Concepts
- **Bucket Name**: Globally unique identifier (e.g., `lab02-yourname-2025`)
- **Location**: Region or multi-region setting that determines where data is stored
- **Storage Class**: Affects pricing and redundancy (Standard, Nearline, Coldline, Archive)
- **Objects**: Files stored in the bucket

### What You Do in the Lab
- Create a bucket via Console and gcloud CLI
- Upload text or HTML files to the bucket
- List and delete objects using the CLI
- Explore bucket properties like public access and retention policies

---

## 📅 Access Control (IAM & ACLs)

### Description
GCS supports two permission systems: **IAM policies** and **Access Control Lists (ACLs)**.

### Key Concepts
- IAM grants roles to users or service accounts at the bucket level
- ACLs can define per-object access (e.g., make a file public)
- Default settings keep all data private

### What You Might Do
- Grant read access to all users for a specific object (e.g., `index.html`)
- Set predefined ACLs using the CLI

---

## 🌐 Static Website Hosting (Optional Challenge)

### Description
GCS buckets can host static websites by enabling **Website Configuration**.

### Key Concepts
- Define `index.html` and optionally `404.html`
- Must allow public read access to view site externally

### What You Might Do
- Upload an `index.html` file
- Enable website configuration and access via public URL

---

## 🔢 CLI Commands Used in This Lab

| Command                                       | Description                            |
|----------------------------------------------|----------------------------------------|
| `gsutil mb`                                   | Create a new bucket                    |
| `gsutil cp`                                   | Copy/upload files to a bucket          |
| `gsutil ls`                                   | List objects or buckets                |
| `gsutil rm`                                   | Delete objects                         |
| `gsutil rb`                                   | Remove the bucket                      |
| `gsutil iam` / `gsutil acl`                   | Set bucket permissions                 |

---

## ✅ Summary

| Resource              | Purpose                                  | Created Using    |
|-----------------------|-------------------------------------------|------------------|
| Cloud Storage Bucket  | Store and manage files                   | Console & CLI    |
| Objects               | Files stored in the bucket                | Uploaded         |
| Access Control (ACL)  | Define who can read/write objects         | CLI / Console    |
| Website Configuration | Enables static site hosting               | Optional         |

This lab offers a hands-on introduction to object storage in GCP, preparing you to build solutions involving static content, file hosting, backups, and more. Mastering GCS is essential for both developers and DevOps engineers!