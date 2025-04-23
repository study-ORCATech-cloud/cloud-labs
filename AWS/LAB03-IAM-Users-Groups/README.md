# AWS LAB03: Resource Overview – IAM Users, Groups, and Policies

## 🧭 What Resources Are We Working With?

In **AWS LAB03**, you’re learning to manage **Identity and Access Management (IAM)** — the core AWS service used to control access to your cloud resources. IAM is vital for secure cloud operations, especially in team and production environments.

This lab introduces three key IAM resources: **users**, **groups**, and **policies**.

---

## 👤 IAM User

### Description
An IAM User represents a single person or application that interacts with AWS. Each user can have its own credentials and permissions.

### Key Concepts
- **Username**: A unique identifier within your AWS account
- **Programmatic Access**: Allows access via CLI, SDKs, or APIs using access keys
- **Console Access** (optional): Web login access to the AWS Console

### What You Do in the Lab
- Create a new user via Console and CLI
- Assign the user to a group
- Generate access keys for CLI/API usage

---

## 👥 IAM Group

### Description
An IAM Group is a collection of IAM Users. Policies assigned to a group apply to all its users.

### Key Concepts
- Simplifies permissions management
- Useful for teams, roles, or access levels (e.g., developers, read-only users)
- Policies can be attached to the group instead of each user individually

### What You Do in the Lab
- Create a group via Console and CLI
- Attach a managed policy (e.g., `AmazonS3ReadOnlyAccess`, `AmazonEC2ReadOnlyAccess`)
- Add users to the group

---

## 📜 IAM Policy

### Description
Policies define the permissions for users, groups, and roles. IAM supports AWS-managed, customer-managed, and inline policies.

### Key Concepts
- **Managed Policies**: Reusable and maintained by AWS (e.g., `AmazonS3FullAccess`)
- **Custom Policies**: JSON documents created for specific needs
- **Least Privilege Principle**: Only give the minimum permissions needed

### What You Do in the Lab
- Attach a managed policy to a group
- (Optional Challenge) Create a custom policy for specific bucket access

---

## 🔐 Access Key & Secret Key

### Description
Programmatic users use access keys to authenticate when calling AWS APIs (CLI, SDK, etc.).

### What You Do in the Lab
- Generate access keys for a user
- Use them to configure a named AWS CLI profile
- Test restricted access based on the user's group policy

---

## ✅ Summary

| Resource     | Purpose                                         | Created Using    |
|--------------|--------------------------------------------------|------------------|
| IAM User     | Individual identity to access AWS               | Console & CLI    |
| IAM Group    | Collective permission manager for users         | Console & CLI    |
| IAM Policy   | Permissions to define what actions are allowed  | Console & CLI    |
| Access Key   | Enables CLI/SDK/API authentication              | Console & CLI    |

IAM is a foundational AWS service that supports **security**, **auditing**, and **access control** for your entire AWS environment. This lab gives you hands-on experience with best practices like group-based permissions and least privilege access.