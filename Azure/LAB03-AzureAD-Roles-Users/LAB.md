# LAB03: Manage Users and Roles with Azure Active Directory (Portal & CLI)

## 👤 Lab Overview

In this lab, you'll use **Azure Active Directory (Azure AD)** to create and manage **users**, assign them to **security groups**, and control their access to resources via **role-based access control (RBAC)**. This lab simulates real-world identity and access management in Azure.

---

## 🎯 Objectives

- Create users in Azure AD
- Organize users into a group
- Assign Azure RBAC roles at resource group level
- Test access using the Azure Portal or CLI

---

## 🛠️ Prerequisites

- Azure account with Owner or User Access Admin role
- Azure CLI installed and authenticated

---

## 📋 Part 1: Create Azure AD Users (Portal)

1. Go to **Azure Active Directory > Users > New user**
2. Fill in:
   - Name: `Test User`
   - Username: `testuser01@<yourtenant>.onmicrosoft.com`
   - Assign initial password
3. Create a second user for practice

---

## 👥 Part 2: Create a Security Group

1. Go to **Azure AD > Groups > New group**
2. Group type: **Security**
3. Name: `DevTeam`
4. Add both test users to the group

---

## 🔐 Part 3: Assign RBAC Role

1. Go to **Resource groups > Create new**: `lab03-rg`
2. Navigate to `Access control (IAM)`
3. Click **+ Add > Add role assignment**
   - Role: Contributor
   - Assign access to: `User`, then select a test user
4. Repeat for the security group to see both methods

---

## 💻 Optional CLI Equivalents
```bash
az ad user create --display-name "Test User" --user-principal-name testuser01@<tenant>.onmicrosoft.com --password P@ssw0rd123
az ad group create --display-name DevTeam --mail-nickname devteam
az ad group member add --group DevTeam --member-id <user-object-id>
az role assignment create --assignee <user-id> --role Contributor --scope /subscriptions/<sub-id>/resourceGroups/lab03-rg
```

---

## 🧪 Challenge

- Create a custom role with restricted permissions (e.g., read-only storage access)
- Assign it to a user or group and test it

---

## 🧹 Cleanup

- Delete created users, group, and resource group

---

## ✅ Summary

You've now used Azure AD and RBAC to manage access to Azure resources, a key capability for enforcing organizational access control and managing teams effectively in the cloud.