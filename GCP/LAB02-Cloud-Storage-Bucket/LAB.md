# LAB02: Create and Manage Cloud Storage Buckets (GCP Console & gcloud CLI)

## 🧠 Lab Overview

In this lab, you’ll explore **Google Cloud Storage (GCS)**—a scalable object storage service used for storing files such as text, images, videos, and backups. You will learn how to create buckets, upload files, configure access, and optionally enable static website hosting using both the **GCP Console** and the **gcloud CLI**.

---

## 🎯 Objectives

- Create a Cloud Storage bucket
- Upload and manage objects (files)
- Use access controls and bucket settings
- (Optional) Host a static website using GCS

---

## 🛠️ Prerequisites

- GCP account ([sign up here](https://cloud.google.com/free))
- Billing enabled
- gcloud CLI installed and initialized (`gcloud init`)
- Project selected in CLI: `gcloud config set project <your-project-id>`

---

## 📄 Part 1: Create Storage Bucket via GCP Console

1. Go to the [Cloud Storage Browser](https://console.cloud.google.com/storage/browser)
2. Click **"Create"**
3. Configure the bucket:
   - **Name**: Must be globally unique (e.g., `lab02-yourname-2025`)
   - **Region**: Choose a region or multi-region (e.g., `us-central1`)
   - **Storage Class**: Standard
   - Leave default access settings (Uniform access)
4. Click **Create**

### Upload a File
1. Click the bucket name > Click **Upload files**
2. Select a `.txt` or `.html` file
3. File will be uploaded as an object into the bucket

---

## 💻 Part 2: Use gcloud CLI for the Same Workflow

### Create Bucket
```bash
gcloud storage buckets create gs://lab02-cli-bucket-2025 \
  --location=us-central1
```

### Upload File
```bash
echo "Hello from GCP CLI" > hello.txt
gcloud storage cp hello.txt gs://lab02-cli-bucket-2025/
```

### List Files in Bucket
```bash
gcloud storage ls gs://lab02-cli-bucket-2025
```

### Delete Object
```bash
gcloud storage rm gs://lab02-cli-bucket-2025/hello.txt
```

### Delete Bucket
```bash
gcloud storage buckets delete gs://lab02-cli-bucket-2025
```

---

## 🧪 Optional Challenge: Static Website Hosting

1. Upload a file named `index.html`
2. Change access so it is **publicly readable**:
```bash
gcloud storage objects add-iam-policy-binding gs://lab02-cli-bucket-2025/index.html \
  --member=allUsers \
  --role=roles/storage.objectViewer
```

3. Enable website configuration (set in GCP Console):
   - Go to bucket > **Edit website configuration**
   - Set `index.html` as the main page
4. Access via the bucket's **public URL**:
   - Format: `https://storage.googleapis.com/<bucket-name>/index.html`

---

## 🌀 Cleanup

```bash
gcloud storage buckets delete gs://lab02-cli-bucket-2025
```
Or delete from the GCP Console.

---

## ✅ Summary

In this lab, you:
- Created and managed GCP storage buckets using Console and CLI
- Uploaded and managed blob objects
- (Optionally) hosted a static website

This lab introduces core cloud storage workflows that will be foundational for more advanced data, website, and automation use cases in GCP. Great job!

