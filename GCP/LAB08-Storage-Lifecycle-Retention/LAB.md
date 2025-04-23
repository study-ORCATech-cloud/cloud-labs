# LAB08: Automate Object Storage Retention with Lifecycle Policies

## 🔁 Lab Overview

In this lab, you’ll manage object lifecycle in **Cloud Storage** by configuring **lifecycle rules** and **retention policies** to automate transitions and deletions.

---

## 🎯 Objectives

- Create a Cloud Storage bucket
- Define lifecycle rules to move/delete data
- Test retention policy enforcement

---

## 🛠️ Prerequisites

- GCP project with billing enabled
- Basic terminal or Console access

---

## 📁 Part 1: Create a Storage Bucket

1. Go to **Cloud Storage > Buckets > Create Bucket**
2. Name: `lab08-bucket-<unique-id>`
3. Location: `us-central1`, Storage class: **Standard**
4. Leave defaults or disable public access

---

## ⚙️ Part 2: Set Lifecycle Rules

1. Go to the bucket > **Lifecycle > Add Rule**
2. Rule 1:
   - Action: **Set storage class to Coldline**
   - Condition: **Age > 30 days**
3. Rule 2:
   - Action: **Delete object**
   - Condition: **Age > 90 days**

---

## ⏳ Part 3: Configure Retention Policy

1. Go to **Retention** tab in bucket settings
2. Set retention period: **60 days**
3. Save changes
4. Try to delete an object (before 60 days) to test restriction

---

## 🧪 Challenge

- Add a condition to apply lifecycle rules only to objects with a prefix (e.g., `archive/`)
- Test object overwrite with versioning enabled

---

## 🧹 Cleanup

- Remove lifecycle rules
- Delete the bucket to avoid charges

---

## ✅ Summary

You’ve now configured automated lifecycle rules and retention policies in Cloud Storage, allowing for scalable, compliant, and cost-effective data management.

