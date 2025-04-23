# GCP LAB07: Resource Overview – Cloud SQL (PostgreSQL)

## 🗄️ What Resources Are We Working With?

In **GCP LAB07**, you'll deploy a fully managed **Cloud SQL** instance running **PostgreSQL**. This lab focuses on secure database provisioning, access control, and query execution.

---

## 🧮 Cloud SQL Instance

### Description
Cloud SQL is a managed service for relational databases like MySQL, PostgreSQL, and SQL Server.

### Key Concepts
- **Instance Tier**: Defines CPU and RAM
- **Backups and HA**: Enable daily backups and failover options
- **PostgreSQL Settings**: Customizable parameters

### What You Do in the Lab
- Deploy a PostgreSQL database
- Configure root password and network access

---

## 🔐 Authorized Networks

### Description
Allows only specific IP addresses to connect to the database.

### What You Do in the Lab
- Add your public IP to the authorized list

---

## 🔌 Client Connection

### Description
Connect to Cloud SQL using built-in SQL client, Cloud Shell, or third-party tools like DBeaver.

### What You Do in the Lab
- Use `psql` or a GUI tool to query the database

---

## ✅ Summary

| Resource              | Purpose                                          | Created Using    |
|-----------------------|--------------------------------------------------|------------------|
| Cloud SQL (PostgreSQL)| Managed relational DB hosting                    | Console & CLI    |
| Authorized Network    | Restricts which IPs can connect                  | Console          |
| PostgreSQL Client     | Connect and run queries                          | CLI or GUI Tools |

This lab provides a real-world example of deploying and connecting to production-ready database infrastructure on GCP.

