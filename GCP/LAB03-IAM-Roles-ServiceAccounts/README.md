# GCP LAB03: Resource Overview – IAM Roles and Service Accounts

## 🛡️ What Resources Are We Working With?

In **GCP LAB03**, you will explore **Identity and Access Management (IAM)** in Google Cloud. This lab introduces **IAM roles** and **service accounts**, key elements in controlling access to resources and enabling secure automation.

---

## 👥 IAM Roles

### Description
Roles define the set of permissions a user or service can have within a project.

### Key Concepts
- **Basic Roles**: Owner, Editor, Viewer
- **Predefined Roles**: Granular roles for specific services (e.g., `roles/storage.objectViewer`)
- **Custom Roles**: Create your own set of permissions (advanced)

### What You Do in the Lab
- Assign predefined roles to users
- Understand the scope of role inheritance (organization, folder, project)

---

## 🤖 Service Accounts

### Description
Service accounts are special Google accounts used by applications or VMs to interact with Google Cloud services.

### Key Concepts
- **Identity for automation**
- **Attached to resources (e.g., VMs)**
- **Uses keys or default credentials for authentication**

### What You Do in the Lab
- Create a service account for a virtual machine
- Assign limited permissions to follow least privilege principle

---

## 🔐 IAM Policy Bindings

### Description
IAM policy bindings define which identities have which roles on a resource.

### What You Do in the Lab
- Add or remove IAM bindings for users and service accounts using `gcloud` and the Console

---

## ✅ Summary

| Resource           | Purpose                                            | Created Using    |
|--------------------|----------------------------------------------------|------------------|
| IAM Roles          | Define access permissions                         | Console & CLI    |
| Service Account    | Identity for automation and workloads             | Console & CLI    |
| IAM Binding        | Links an identity to a role on a resource         | Console & CLI    |

IAM is foundational for managing security and access in GCP. This lab gives you practical experience applying best practices like least privilege and scoped permissions.