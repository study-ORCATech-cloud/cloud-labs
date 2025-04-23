# LAB08: Automate Blob Retention with Lifecycle Policies in Azure

## 🔁 Lab Overview

In this lab, you'll implement automated **lifecycle management rules** for blobs stored in **Azure Storage**. You’ll reduce costs by moving infrequently accessed data to cooler tiers and eventually deleting obsolete files.

---

## 🎯 Objectives

- Create a Blob container with uploaded files
- Enable and configure lifecycle management rules
- Transition and delete data based on age

---

## 🛠️ Prerequisites

- Azure subscription
- Azure CLI or Portal access

---

## 📁 Part 1: Create Blob Container and Upload Files

1. Go to **Storage Accounts > Create**
2. Name: `lab08storageacct`
3. Enable blob public access: Disabled
4. Navigate to the account > **Containers** > `+ Container`
5. Name: `data`
6. Upload files manually or via CLI
```bash
az storage blob upload --container-name data --file example.txt --name example.txt --account-name lab08storageacct
```

---

## ⚙️ Part 2: Create Lifecycle Management Rule

1. Go to **Data management > Lifecycle management**
2. Create rule: `move-to-cool`
3. Conditions:
   - Base rule on **last modified**
   - If age > 30 days → Move to Cool tier
   - If age > 90 days → Delete

---

## 🧪 Challenge

- Create tag-based rule: only apply to blobs with tag `type=archive`
- Test rule effectiveness over time using simulated file aging (metadata editing)

---

## 💻 Optional CLI
```bash
az storage account management-policy create \
  --account-name lab08storageacct \
  --policy file://policy.json
```
*(Prepare a `policy.json` file with rule definition.)*

---

## 🧹 Cleanup

- Delete lifecycle rules and storage account

---

## ✅ Summary

You now know how to automatically manage blob retention in Azure, a key part of storage optimization strategies in scalable and cost-conscious cloud systems.

