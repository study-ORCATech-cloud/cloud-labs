# AWS LAB08: Resource Overview – S3 Lifecycle Rules & Versioning

## 🔄 What Resources Are We Working With?

In **AWS LAB08**, you’ll implement **lifecycle management** and **object versioning** in Amazon S3 to optimize storage costs and preserve object history. This lab focuses on automating data retention and recovery processes.

---

## 🗃️ Amazon S3 (Enhanced Usage)

### Description
S3 is used here for more than just storage. Lifecycle rules and versioning introduce automation and data durability features.

### Key Concepts
- **Object Versioning**: Keeps multiple versions of an object
- **Lifecycle Rules**: Automatically transitions or expires objects
- **Storage Classes**: Move objects to Glacier, IA, Deep Archive, etc.

### What You Do in the Lab
- Enable versioning on an S3 bucket
- Create and apply a lifecycle rule to:
  - Transition objects to Glacier/IA after X days
  - Permanently delete old versions after Y days

---

## 📑 Lifecycle Configuration

### Description
Lifecycle rules define actions based on object age, prefix, or tags.

### What You Do in the Lab
- Create a rule for auto-transition to infrequent storage
- Expire old versions to control storage costs

---

## 🧪 Versioning

### Description
Enables historical tracking and recovery of deleted or changed objects.

### What You Do in the Lab
- Enable versioning on the bucket
- Upload and overwrite the same file to generate versions
- Restore an older version

---

## ✅ Summary

| Resource              | Purpose                                           | Created Using    |
|-----------------------|--------------------------------------------------|------------------|
| S3 Bucket             | Stores versioned objects                         | Console & CLI    |
| Object Versioning     | Maintains history of object changes              | Console & CLI    |
| Lifecycle Rule        | Automates storage class transitions & deletion   | Console & CLI    |

This lab shows how to scale your S3 usage with automation and resiliency, reducing manual effort and storage costs in production systems.