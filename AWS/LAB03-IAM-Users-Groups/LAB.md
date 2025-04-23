# LAB03: AWS IAM – Users, Groups, and Policies (Console & CLI)

## 🧠 Lab Overview

In this lab, you'll explore **AWS Identity and Access Management (IAM)**. IAM allows you to securely manage access to AWS services and resources. You’ll learn how to create IAM users and groups, attach policies, and test access permissions using both the **AWS Management Console** and **AWS CLI**.

IAM is essential for implementing the principle of least privilege, managing team access, and enabling automation securely.

---

## 🎯 Objectives

By the end of this lab, you will:

- Understand the core concepts of IAM
- Create IAM users and groups using the Console and CLI
- Attach policies (e.g., `AmazonS3ReadOnlyAccess`)
- Test user access to AWS services

---

## 🛠️ Prerequisites

- AWS account
- AWS CLI installed and configured as an admin (`aws configure`)
- IAM full access for your current user or role

---

## 📋 Part 1: Create IAM User and Group (Console)

1. **Login to AWS Console** > go to **IAM**
2. **Create Group**:
   - Navigate to **User Groups** > **Create group**
   - Name it `lab03-group`
   - Attach policy: `AmazonS3ReadOnlyAccess`
3. **Create User**:
   - Go to **Users** > **Add user**
   - Username: `lab03-user`
   - Access type: **Programmatic access** (optional: Console access)
   - Add user to group: `lab03-group`
   - Download `.csv` file with access key and secret key
4. **Verify Access** (optional):
   - Log in with the new user if console access was enabled
   - Try accessing S3 and verify read-only access

---

## 💻 Part 2: IAM Management via AWS CLI

### 1. Create IAM Group
```bash
aws iam create-group --group-name lab03-cli-group
```

### 2. Attach Policy to Group
```bash
aws iam attach-group-policy \
  --group-name lab03-cli-group \
  --policy-arn arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess
```

### 3. Create IAM User
```bash
aws iam create-user --user-name lab03-cli-user
```

### 4. Add User to Group
```bash
aws iam add-user-to-group \
  --user-name lab03-cli-user \
  --group-name lab03-cli-group
```

### 5. Create Access Keys (Programmatic Access)
```bash
aws iam create-access-key --user-name lab03-cli-user
```
> Save these keys securely – they allow API/CLI access!

### 6. Test Permissions (Optional)
Use the access key and secret to configure a new AWS profile:
```bash
aws configure --profile test-user
```
Try running a command:
```bash
aws ec2 describe-instances --profile test-user
```
You should receive permission denied or limited output based on read-only policy.

---

## 🧪 Challenge Task

- Create a custom policy that allows read access to a specific S3 bucket only
- Attach it to a new group and assign a user
- Try listing files in that specific bucket with the new user’s credentials

---

## 🧹 Cleanup

To avoid clutter:
```bash
aws iam remove-user-from-group --user-name lab03-cli-user --group-name lab03-cli-group
aws iam delete-user --user-name lab03-cli-user
aws iam detach-group-policy --group-name lab03-cli-group --policy-arn arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess
aws iam delete-group --group-name lab03-cli-group
```
Or delete users/groups via Console.

---

## ✅ Summary

You’ve now:
- Created IAM users and groups
- Attached AWS managed policies
- Verified access behavior through CLI and Console

Understanding IAM is crucial for security, governance, and team collaboration in AWS. Great job taking another step forward!

