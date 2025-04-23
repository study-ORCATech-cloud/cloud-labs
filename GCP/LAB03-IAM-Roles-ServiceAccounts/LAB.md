# LAB03: Manage IAM Roles and Service Accounts in GCP

## 🛡️ Lab Overview

In this lab, you will gain hands-on experience with **Identity and Access Management (IAM)** in Google Cloud. You’ll create and assign roles to users and service accounts, enforce least privilege, and enable secure access to resources.

---

## 🎯 Objectives

- Create IAM users and service accounts
- Assign predefined roles at project scope
- Test permission behavior using the Console and CLI

---

## 🛠️ Prerequisites

- GCP account with Owner or IAM Admin permissions
- Project created and selected in Cloud Shell or gcloud config

---

## 👤 Part 1: Add IAM User and Assign Role

1. Navigate to **IAM & Admin > IAM**
2. Click **Grant Access**
3. Email: `<test-user-email>`
4. Role: **Viewer** or `roles/compute.viewer`
5. Save and verify the user is listed

---

## 🤖 Part 2: Create a Service Account

1. Go to **IAM & Admin > Service Accounts**
2. Click **Create Service Account**
   - Name: `lab03-svc`
   - ID: auto-generated
3. Grant access to project
   - Role: **Storage Object Viewer** (`roles/storage.objectViewer`)

---

## 🔑 Part 3: Generate and Download Service Account Key

1. In the Service Account settings, go to **Keys > Add Key > Create new key**
2. Choose JSON format and download it
3. Use with SDK or automation tools for authentication

---

## 💻 Part 4: Test Access Using gcloud CLI

```bash
# Set active project (if not already set)
gcloud config set project <your-project-id>

# Impersonate service account
gcloud auth activate-service-account --key-file=<path-to-json-key>

# Try to list storage buckets
gcloud storage buckets list
```

---

## 🧪 Challenge Task

- Create a custom role that includes only `resourcemanager.projects.get`
- Assign it to the user or service account
- Confirm it works and restricts access to only project metadata

---

## 🧹 Cleanup

- Remove the test IAM user and role bindings
- Delete the service account and key

---

## ✅ Summary

You’ve now created and managed IAM roles and service accounts, key to implementing secure and scalable access control in GCP.