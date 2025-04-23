# LAB07: Launch a MySQL Database with Amazon RDS (Console & CLI)

## 🗄️ Lab Overview

In this lab, you’ll deploy a **MySQL database** using **Amazon RDS**, AWS's managed relational database service. You’ll learn how to configure database parameters, enable access with a security group, and connect to the DB instance using a SQL client.

---

## 🎯 Objectives

- Create an Amazon RDS instance (MySQL)
- Set up credentials and networking
- Connect to the database using a MySQL client
- View metrics using CloudWatch

---

## 🛠️ Prerequisites

- AWS account
- IAM permissions for RDS and networking
- MySQL client installed locally

---

## 🏗️ Part 1: Create RDS MySQL Instance

1. Go to **RDS > Databases > Create database**
2. Engine: **MySQL**
3. Template: **Free tier**
4. DB instance identifier: `lab07-db`
5. Master username/password: `admin / password1234` (demo purposes only)
6. DB instance class: `db.t3.micro`
7. Storage: General Purpose (20 GB)
8. Enable public access (only for this lab)

---

## 🔐 Part 2: Configure Networking

1. Choose VPC and create new subnet group if needed
2. Create or choose **Security Group**:
   - Allow inbound access on port 3306 (MySQL) from your IP

---

## 🔌 Part 3: Connect to the Database

After instance is in **Available** status:
1. Copy **Endpoint** and port from RDS dashboard
2. From terminal:
```bash
mysql -h <endpoint> -u admin -p
```
3. Create a table, insert some rows:
```sql
CREATE DATABASE labdata;
USE labdata;
CREATE TABLE messages (id INT, content VARCHAR(255));
INSERT INTO messages VALUES (1, 'Hello from RDS!');
SELECT * FROM messages;
```

---

## 📈 Part 4: Monitor with CloudWatch

1. Go to **CloudWatch > Metrics > RDS**
2. View CPU utilization, DB connections, and storage
3. Optionally create an alarm for storage or CPU

---

## 🧪 Challenge

- Enable automated backups and set retention to 7 days
- Explore multi-AZ deployment for high availability

---

## 🧹 Cleanup

- Delete the RDS instance (ensure **final snapshot** is skipped for cost-saving)
- Delete security group if not reused

---

## ✅ Summary

You successfully launched a MySQL database using Amazon RDS, secured access, connected via a client, and explored monitoring options — all without managing the underlying infrastructure!

