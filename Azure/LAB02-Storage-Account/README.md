# Azure LAB02: Resource Overview

## 🔧 What Resources Are We Working With?

In **Azure LAB02**, we work with **Azure Storage Accounts**, specifically focusing on **Blob Storage**, which is optimized for storing massive amounts of unstructured data such as text or binary files. This lab introduces you to storage containers, blobs, and access management.

---

## 📂 Storage Account

### Description
A storage account is a namespace in Azure for all types of data storage: blobs, files, queues, and tables. It provides a unique endpoint and access management controls.

### Key Concepts
- **Storage Account Name**: Must be globally unique
- **Region**: Determines the physical location of the storage
- **Performance Tier**: Standard or Premium (this lab uses Standard)
- **Redundancy**: Controls replication type (LRS, GRS, ZRS, etc.)

### What You Do in the Lab
- Create a storage account using Azure Portal and CLI
- Choose Standard performance and LRS redundancy

---

## 🏦 Blob Container

### Description
Blob containers organize blobs within a storage account. A single container can store millions of blobs.

### Key Concepts
- **Container Name**: Must be lowercase, no special characters
- **Access Level**: Private, Blob (anonymous read), or Container

### What You Do in the Lab
- Create a container for storing blobs (e.g., `labcontainer`)
- Set access level (keep it private for now or use Blob for public file access)

---

## 📅 Blob (Object)

### Description
Blobs are the actual files you store in a container. Types include block blobs (text files, media), append blobs (log files), and page blobs (disks).

### Key Concepts
- Most common: **Block Blobs**
- Can upload/download via Azure Portal, Azure CLI, or `azcopy`

### What You Do in the Lab
- Upload a text or HTML file into the container
- View file details and test file access (if public)

---

## 🔢 CLI Commands Used in This Lab

| Command                                             | Description                                  |
|----------------------------------------------------|----------------------------------------------|
| `az storage account create`                        | Create a new storage account                |
| `az storage container create`                      | Create a blob container                     |
| `az storage blob upload`                           | Upload a file to the container              |
| `az storage blob list`                             | List blobs in a container                   |
| `az storage blob delete`                           | Delete blobs                                |
| `az storage account delete`                        | Remove storage account                      |

> ⚠️ Many of these commands require a `--account-name` and either a **key** or **connection string**.

---

## 🌐 Optional: Static Website Hosting

Azure Blob Storage supports static website hosting.

### Key Concepts
- You must enable the **static website option** in the portal or CLI
- Set index document name (e.g., `index.html`)
- Upload `index.html` and optionally `404.html`
- Configure public access to serve files

### What You Might Do
- Enable static website and test access via the public endpoint

---

## ✅ Summary

| Resource            | Purpose                                     | Created Using    |
|---------------------|----------------------------------------------|------------------|
| Storage Account     | Top-level resource for all storage types     | Portal & CLI     |
| Blob Container      | Logical grouping of blobs                    | Portal & CLI     |
| Blob (Object)       | Individual file stored in the container      | Portal & CLI     |
| Static Website (opt)| Serve web files publicly                     | Portal & CLI     |

This lab sets the foundation for working with file and object storage in Azure—a must-have skill for DevOps workflows, web hosting, and backup scenarios!