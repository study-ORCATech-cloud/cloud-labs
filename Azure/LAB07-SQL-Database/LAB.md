# LAB07: Deploy and Connect to Azure SQL Database

## 🗃️ Lab Overview

In this lab, you will provision a fully managed **Azure SQL Database**, secure access using firewall rules, and connect using a SQL client. This is a real-world scenario for developers and DBAs who want a high-availability, low-maintenance relational database in Azure.

---

## 🎯 Objectives

- Create an Azure SQL Server and Database
- Configure firewall rules to allow secure external access
- Connect via SQL client and perform simple queries

---

## 🛠️ Prerequisites

- Azure subscription
- SQL client (Azure Data Studio, SSMS, or `sqlcmd`)

---

## 🧱 Part 1: Create SQL Database (Portal)

1. Go to **SQL databases > Create**
2. Database name: `lab07db`
3. Create new logical server:
   - Name: `lab07sqlserver`
   - Admin login: `adminuser`
   - Password: `SecureP@ss123`
4. Compute + storage: Basic tier (or Free Trial tier)
5. Enable Public Access (lab only)

---

## 🔐 Part 2: Configure Firewall Rules

1. After deployment, go to the SQL server resource
2. Under **Networking**, add your IP to the firewall
3. (Optional) Allow Azure services to access the server

---

## 🔌 Part 3: Connect and Query

1. Copy the server name from the portal (e.g., `lab07sqlserver.database.windows.net`)
2. Connect using a SQL tool:
```bash
sqlcmd -S lab07sqlserver.database.windows.net -U adminuser -P 'SecureP@ss123' -d lab07db
```
3. Run SQL commands:
```sql
CREATE TABLE customers (id INT, name VARCHAR(100));
INSERT INTO customers VALUES (1, 'Azure Labs');
SELECT * FROM customers;
```

---

## 🧪 Challenge

- Explore geo-replication options
- Enable automated backups and restore a backup

---

## 🧹 Cleanup

- Delete the SQL database and server

---

## ✅ Summary

You've now created a secure, managed relational database using Azure SQL. This lab introduces best practices for provisioning and access management in a production environment.

