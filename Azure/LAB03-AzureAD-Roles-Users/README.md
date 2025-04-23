# Azure LAB03: Resource Overview – Azure Active Directory (AD) Roles and Users

## 🧭 What Resources Are We Working With?

In **Azure LAB03**, you’ll work with **Azure Active Directory (Azure AD)**, Microsoft’s cloud-based identity and access management service. You’ll create users, assign them to groups, and configure role-based access control (RBAC) to secure access to Azure resources.

This lab focuses on **user management**, **role assignments**, and **security best practices**.

---

## 👤 Azure AD Users

### Description
Azure AD users represent individuals in your organization who need access to resources and applications.

### Key Concepts
- **Username / UPN**: Often in the format `user@yourdomain.onmicrosoft.com`
- **Authentication**: Azure handles identity and token-based access

### What You Do in the Lab
- Create individual users manually in the Azure Portal
- Configure properties like name, password, and role

---

## 👥 Azure AD Groups

### Description
Groups are used to manage sets of users and simplify permissions management.

### Key Concepts
- **Security groups**: Used for granting access to resources
- **Microsoft 365 groups**: Also include collaborative tools (not used in this lab)

### What You Do in the Lab
- Create security groups
- Add users to a group for access control

---

## 🔐 Role-Based Access Control (RBAC)

### Description
RBAC defines what actions users and groups can perform in Azure.

### Key Concepts
- **Built-in roles**: Reader, Contributor, Owner, etc.
- **Custom roles**: Define specific permissions (advanced use)
- **Scope**: Can be assigned at the subscription, resource group, or resource level

### What You Do in the Lab
- Assign the **Reader** or **Contributor** role to a user or group
- Limit access scope to a specific resource group

---

## ✅ Summary

| Resource          | Purpose                                           | Created Using    |
|-------------------|--------------------------------------------------|------------------|
| Azure AD User     | Represents a person or service in the directory  | Azure Portal     |
| Azure AD Group    | Collection of users for simplified permissions   | Azure Portal     |
| RBAC Role         | Grants access based on assigned privileges       | Azure Portal & CLI|

Understanding Azure AD and RBAC is foundational for enforcing **least privilege access**, **delegated administration**, and **compliance** in any secure cloud environment.

