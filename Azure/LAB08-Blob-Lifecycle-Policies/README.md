# Azure LAB08: Resource Overview – Blob Storage Lifecycle Management

## 🔁 What Resources Are We Working With?

In **Azure LAB08**, you’ll automate blob data retention with **lifecycle management rules** in a storage account. These rules help reduce costs by automatically moving or deleting blobs based on age or tags.

---

## 📂 Azure Blob Storage (Extended)

### Description
This lab builds on basic blob storage to introduce automation and cost control via policies.

### Key Concepts
- **Lifecycle Policies**: Define automated transitions and deletions
- **Access Tiers**: Hot, Cool, Archive (lower tiers = lower cost, higher latency)
- **Blob Index Tags**: Metadata used to filter blobs for rules

### What You Do in the Lab
- Create a lifecycle rule to move blobs to cool/archive after X days
- Automatically delete blobs older than Y days

---

## ⚙️ Lifecycle Management Rule

### Description
Rules applied at the storage account level that evaluate and manage blobs based on age or tags.

### What You Do in the Lab
- Define rule to transition blobs >30 days old to Cool
- Set deletion for blobs >90 days
- (Optional) Apply rules only to blobs with specific tags (e.g., `archive=true`)

---

## ✅ Summary

| Resource                 | Purpose                                          | Created Using    |
|--------------------------|--------------------------------------------------|------------------|
| Blob Storage             | Stores unstructured data                         | Portal & CLI     |
| Lifecycle Rule           | Automates data retention policies                | Portal & CLI     |
| Access Tier (Cool/Archive)| Cost-optimize storage based on usage patterns   | Portal           |
| Index Tags (Optional)    | Used to target lifecycle rules                   | CLI / SDK        |

This lab introduces critical data governance and cost optimization techniques essential in production environments.

