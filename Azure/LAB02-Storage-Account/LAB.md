# LAB02: Create and Manage Azure Blob Storage (Portal & CLI)

## 🧠 Lab Overview

In this lab, you’ll learn how to use **Azure Blob Storage** by working with **Storage Accounts**, **Blob Containers**, and **Blob Objects** using both the **Azure Portal** and **Azure CLI**. You’ll also explore optional static website hosting capabilities.

Azure Blob Storage is essential for storing unstructured data such as images, documents, logs, and backups. It is widely used in DevOps, data pipelines, and web hosting.

---

## 🎯 Objectives

By the end of this lab, you will:

- Create a Storage Account
- Create a Blob Container
- Upload and manage files (blobs)
- Explore access control options
- (Optional) Enable static website hosting

---

## 🛠️ Prerequisites

- Azure account ([sign up here](https://azure.microsoft.com/en-us/free))
- Azure CLI installed and configured (`az login`)
- Basic understanding of cloud storage and terminal usage

---

## 📄 Part 1: Create Storage Account (Azure Portal)

1. Go to [https://portal.azure.com](https://portal.azure.com)
2. Search for and open **Storage accounts** > **Create**
3. Configure:
   - **Subscription**: Your active subscription
   - **Resource Group**: Create `lab02-rg` if needed
   - **Storage Account Name**: Must be globally unique (e.g., `lab02storename2025`)
   - **Region**: Choose closest region
   - **Performance**: Standard
   - **Redundancy**: LRS
4. Click **Review + Create** > **Create**

---

## 📁 Part 2: Create Blob Container & Upload Files (Portal)

1. Navigate to your storage account > **Containers** > **+ Container**
2. Name it `labcontainer`
3. Set **Public access level**:
   - Use **Private** for default security (public access disabled)
4. Click the container, then **Upload** any `.txt` or `.html` file
5. After uploading, click on the blob to view details or download it

---

## 💻 Part 3: Same Workflow via Azure CLI

### 1. Create Storage Account
```bash
az group create --name lab02-rg --location eastus
az storage account create \
  --name lab02storagecli \
  --resource-group lab02-rg \
  --location eastus \
  --sku Standard_LRS \
  --kind StorageV2
```

### 2. Get Access Key (for CLI use)
```bash
az storage account keys list \
  --account-name lab02storagecli \
  --resource-group lab02-rg \
  --query [0].value --output tsv
```
Export the key:
```bash
export AZURE_STORAGE_KEY=<paste-key-here>
```

### 3. Create Container
```bash
az storage container create \
  --name labcontainer \
  --account-name lab02storagecli \
  --account-key $AZURE_STORAGE_KEY
```

### 4. Upload File
```bash
echo "Hello Azure CLI" > hello.txt
az storage blob upload \
  --container-name labcontainer \
  --file hello.txt \
  --name hello.txt \
  --account-name lab02storagecli \
  --account-key $AZURE_STORAGE_KEY
```

### 5. List Blobs
```bash
az storage blob list \
  --container-name labcontainer \
  --account-name lab02storagecli \
  --account-key $AZURE_STORAGE_KEY \
  --output table
```

---

## 🧪 Optional Challenge: Enable Static Website Hosting

1. In the Portal, go to your Storage Account > **Static website** > Enable
2. Set index document name: `index.html`
3. Upload `index.html` into `$web` container
4. Note the **primary endpoint URL** to view your site

Or via CLI:
```bash
az storage blob service-properties update \
  --account-name lab02storagecli \
  --static-website \
  --index-document index.html
```

Upload HTML file:
```bash
az storage blob upload \
  --container-name \$web \
  --file index.html \
  --name index.html \
  --account-name lab02storagecli \
  --account-key $AZURE_STORAGE_KEY
```

---

## 🌀 Cleanup

```bash
az group delete --name lab02-rg --yes --no-wait
```

---

## ✅ Summary

You now know how to:
- Use Azure Blob Storage to store and manage files
- Create storage accounts and containers via Portal and CLI
- Upload/download blobs securely
- (Optionally) Host static websites from Blob Storage

Excellent work advancing your Azure storage skills! 🚀